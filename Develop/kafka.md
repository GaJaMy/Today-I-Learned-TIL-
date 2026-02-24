# Kafka 핵심 정리

## 1. Kafka란?

> **"Kafka는 대용량 실시간 데이터 스트리밍을 위한 분산 이벤트 스트리밍 플랫폼"**

Spring이 "웹 서버 개발을 위한 Java 기반 웹 프레임워크"인 것처럼,  
Kafka는 **이벤트/메시지를 대용량으로 빠르게 처리하는 플랫폼**이다.

---

## 2. Kafka vs 메시지 큐 (RabbitMQ, ActiveMQ)

| 구분 | 메시지 큐 | Kafka |
|------|----------|-------|
| 메시지 소비 후 | 삭제됨 | 설정 기간 동안 보관 |
| 소비 횟수 | 1번 | 여러 번 가능 |
| 구조 | 큐 기반 | 로그 기반 (순차 저장) |
| 주 목적 | 작업 분배, 비동기 처리 | 데이터 스트리밍, 이벤트 소싱 |
| 여러 컨슈머 | 경쟁 소비 | 각자 독립적으로 소비 가능 |

**비유:**
- 메시지 큐 = "일회용 편지함" (읽으면 사라짐)
- Kafka = "계속 기록되는 공개 일지" (누구나 읽을 수 있고 남아있음)

---

## 3. MSA에서 Kafka 활용

### 비동기 이벤트 기반 통신

동기 방식(REST 직접 호출) 대신, **이벤트를 발행하고 구독**하는 방식으로 서비스 간 통신.

```
주문 서비스 → order.created 이벤트 발행 → Kafka
                                              ↓
                                    결제 서비스 (결제 처리)
                                    재고 서비스 (재고 차감)
                                    알림 서비스 (메일/SMS 발송)
```

주문 서비스는 누가 이벤트를 구독하는지 몰라도 됨.

### 장점

- **느슨한 결합**: 서비스들이 서로를 직접 알 필요 없음
- **장애 격리**: 알림 서비스가 다운돼도 주문/결제는 정상 진행, 복구 후 밀린 이벤트 처리
- **확장성**: 새 서비스 추가 시 기존 코드 수정 없이 이벤트만 구독
- **데이터 일관성**: 이벤트 재처리 가능

---

## 4. Kafka vs gRPC (중요!)

MSA에서 서비스 간 통신 방식은 두 가지:

| 구분 | gRPC | Kafka |
|------|------|-------|
| 방식 | 동기 (응답 기다림) | 비동기 (기다리지 않음) |
| 용도 | 즉시 응답 필요한 경우 | 시간 걸리는 작업, 여러 서비스가 독립 처리 |
| 예시 | 재고 확인, 사용자 정보 조회 | 주문 후 결제/알림/통계 처리 |

```
gRPC:
Order Service → (요청) → Payment Service
              ⏱️ 응답 기다림
Order Service ← (응답) ← Payment Service

Kafka:
Order Service → (이벤트 발행) → Kafka
                                   ↓ (나중에 처리)
                             Payment Service
Order Service는 기다리지 않고 다음 작업 진행
```

> 실무에서는 **둘 다 함께 사용**한다.  
> 즉시 응답이 필요한 건 gRPC, 비동기로 처리해도 되는 건 Kafka.

---

## 5. Zookeeper vs KRaft

### Zookeeper (예전 방식)
- Kafka 브로커 클러스터를 **외부에서 관리**해주는 관리자 역할
- 누가 리더인지, 어떤 브로커가 살아있는지 추적
- Kafka만으로는 클러스터 관리가 불가능해서 필수였음

```yaml
# docker-compose 예시 (Zookeeper 방식)
zookeeper:
  image: confluentinc/cp-zookeeper
kafka:
  image: confluentinc/cp-kafka
  depends_on:
    - zookeeper
```

### KRaft (요즘 방식, 추천)
- Kafka 2.8부터 도입, 3.x부터 안정화
- Kafka가 **스스로 클러스터 관리** → Zookeeper 불필요

```yaml
# docker-compose 예시 (KRaft 방식)
kafka:
  image: confluentinc/cp-kafka
  environment:
    KAFKA_PROCESS_ROLES: broker,controller
```

> 학습용이라면 **KRaft 방식**으로 구성 추천.

---

## 6. Kafka 전체 구조

### 핵심 개념 정리

**브로커(Broker) == Kafka 하나** (도커 컨테이너 1개 = 브로커 1개)

**클러스터(Cluster) == 브로커 여러 개가 묶인 것**

```
┌──────── Kafka Cluster (하나의 Kafka 시스템) ────────┐
│                                                    │
│  [Broker 1 컨테이너]  [Broker 2 컨테이너]  [Broker 3 컨테이너]  │
│      (9092 포트)          (9093 포트)          (9094 포트)   │
└────────────────────────────────────────────────────┘
```

클러스터를 쓰는 이유는 **고가용성** - 브로커 1개가 죽어도 나머지가 계속 서비스.  
학습/로컬 개발에서는 **브로커 1개짜리 단일 클러스터**로 충분.

### 구성 요소

| 개념 | 설명 |
|------|------|
| **Broker** | 실제 메시지를 저장하고 전달하는 서버 (Kafka 하나) |
| **Topic** | 메시지를 분류하는 카테고리 (예: `order.created`, `payment.completed`) |
| **Partition** | Topic을 쪼갠 단위, 병렬 처리를 위해 존재 |
| **Producer** | 메시지를 Kafka에 발행하는 쪽 (주문 서비스 등) |
| **Consumer** | 메시지를 Kafka에서 가져가는 쪽 (결제 서비스 등) |
| **Consumer Group** | 같은 역할을 하는 Consumer들의 묶음 |
| **Offset** | 파티션 안에서 메시지의 위치(번호), 어디까지 읽었는지 추적 |

---

## 7. 이벤트 발행 원리

### 언제 발행되는가?

**스프링 서버가 올라갈 때가 아니라, 비즈니스 로직이 실행될 때 발행됨.**

```java
@Service
public class OrderService {

    private final KafkaTemplate<String, String> kafkaTemplate;

    public Order createOrder(OrderRequest request) {
        // 1. DB에 주문 저장
        Order order = orderRepository.save(request.toEntity());

        // 2. 주문 저장된 그 순간 이벤트 발행  ← 이 시점!
        kafkaTemplate.send("order.created", order.getId().toString());

        return order;
    }
}
```

### 발행 ~ 저장까지 전체 흐름

```
1. kafkaTemplate.send("order.created", "주문데이터")
       ↓
2. 어느 브로커로 보낼지 결정 (메타데이터 기반, 자동)
       ↓
3. 어느 파티션에 저장할지 결정 (Key 기반 or 라운드로빈)
       ↓
4. 파티션에 offset 붙여서 저장
       ↓
5. Consumer가 폴링하다가 감지 → 메서드 자동 실행
```

### 파티션 결정 방식

**Key 없이 발행 → 라운드로빈**
```java
kafkaTemplate.send("order.created", "주문데이터");
// 파티션 0 → 1 → 2 → 0 ... 순서대로 분배
```

**Key 있이 발행 → Key 해시값으로 항상 같은 파티션**
```java
kafkaTemplate.send("order.created", "user-123", "주문데이터");
// "user-123"의 해시값 % 파티션수 = 항상 같은 파티션 → 순서 보장
```

### 파티션 저장 구조

파티션은 **데이터를 쪼개는 게 아니라, 메시지를 통째로 저장하는 여러 개의 통**이다.

```
order.created Topic
├── Partition 0: [아메리카노 주문(ofs:0), 카페라떼 주문(ofs:1), ...]
├── Partition 1: [바닐라라떼 주문(ofs:0), 콜드브루 주문(ofs:1), ...]
└── Partition 2: [모카 주문(ofs:0), 아이스티 주문(ofs:1), ...]
```

하나의 메시지(아메리카노 주문)가 여러 파티션에 쪼개지는 게 아니라, **통째로 하나의 파티션에만 저장됨.**

---

## 8. Consumer 동작 원리

### 폴링(Polling) 방식

`@KafkaListener`가 백그라운드에서 자동으로 계속 Kafka에 요청함.

```java
@KafkaListener(topics = "order.created", groupId = "payment-group")
public void handleOrderCreated(String message) {
    // 메시지 도착 시 자동 실행
    System.out.println("받은 메시지: " + message);
}
```

```
스프링 서버 시작 (@KafkaListener 등록)
       ↓
백그라운드에서 자동으로 Kafka에 계속 요청
"새 메시지 있어?" → 없음
"새 메시지 있어?" → 없음
"새 메시지 있어?" → 있음! → handleOrderCreated() 자동 실행
```

### 리밸런싱 (Rebalancing)

스프링 서버가 올라오는 순간 Kafka가 자동으로 파티션을 할당해줌.

```
서버 1대:  Consumer 1 → Partition 0, 1, 2 전부 담당

서버 2대:  Consumer 1 → Partition 0, 1
           Consumer 2 → Partition 2

서버 3대:  Consumer 1 → Partition 0
           Consumer 2 → Partition 1
           Consumer 3 → Partition 2
```

> **파티션 수 = 최대 병렬 처리 수**  
> Consumer가 파티션 수보다 많으면 남는 Consumer는 놀게 됨.  
> 처음 토픽 만들 때 파티션 수를 잘 결정하는 게 중요.

---

## 9. 메시지 발행 상세 원리

### 핵심 개념

> **Producer가 모든 걸 미리 결정하고 쏜다. Kafka는 받아서 저장만 한다. 그래서 빠르다.**

### 스프링 서버 시작 시 (1회)

```
bootstrap-servers로 Kafka 접속
        ↓
메타데이터 받아옴 (이걸 Producer가 계속 들고 있음)
"Partition 0 → Broker 1 (9092)"
"Partition 1 → Broker 1 (9092)"
"Partition 2 → Broker 2 (9093)"
"Partition 3 → Broker 2 (9093)"
"Partition 4 → Broker 3 (9094)"
"Partition 5 → Broker 3 (9094)"
```

### 메시지 발행 시 (매번)

```
kafkaTemplate.send("order.created", "아메리카노 주문")
        ↓
① 파티션 결정 (라운드로빈 or Key 해시) → Partition 0
        ↓
② 메타데이터 확인 "Partition 0은 Broker 1 (9092)"
        ↓
③ Broker 1:9092 포트로 직접 전송
        ↓
④ Kafka는 받아서 Partition 0에 저장만 함
```

### 파티션 6개, 브로커 3개일 때 저장 구조

```
Broker 1
├── Partition 0: [아메리카노, 아이스티, ...]
└── Partition 1: [카페라떼, 녹차라떼, ...]

Broker 2
├── Partition 2: [에스프레소, ...]
└── Partition 3: [바닐라라떼, ...]

Broker 3
├── Partition 4: [콜드브루, ...]
└── Partition 5: [모카, ...]
```

### 왜 빠른가?

| 일반 서버 | Kafka |
|----------|-------|
| 메시지 도착 → "어디 저장하지?" 판단 → 저장 | 메시지 도착 → 그냥 저장 |
| 서버가 결정 | Producer가 미리 결정하고 보냄 |
| 느림 | 빠름 |

### bootstrap-servers를 2~3개 적는 이유

```yaml
spring:
  kafka:
    bootstrap-servers: kafka-1:9092, kafka-2:9092  # 2개 권장
```

- 1개만 적으면 그 브로커가 죽었을 때 최초 연결 자체가 안 됨
- 메타데이터만 받아오면 되므로 전체 브로커를 다 적을 필요 없음
- 메타데이터를 받은 후에는 전체 브로커 정보를 자동으로 파악

---

## 10. 학습용 프로젝트 구성

### 추천: 전자상거래 시스템

**서비스 구성:**
- Order Service (주문)
- Payment Service (결제)
- Inventory Service (재고)
- Notification Service (알림)

**Kafka 이벤트 흐름:**
```
주문 생성 → order.created
  ↓
결제 서비스 → payment.completed
  ↓
재고 서비스 → inventory.updated
  ↓
알림 서비스 → 고객에게 알림
```

### 단계별 접근법

| 단계 | 내용 |
|------|------|
| Phase 1 (기본) | 서비스 2개 (주문 + 알림), 단순 이벤트 처리 |
| Phase 2 (중급) | 서비스 3~4개, 이벤트 체인, 에러 핸들링 |
| Phase 3 (고급) | Saga 패턴, Dead Letter Queue, 모니터링 |

### 프로젝트 구조 (멀티 모듈 추천)

혼자 공부할 땐 **멀티 모듈 프로젝트**가 편리함. (Git 하나로 관리 가능)

```
ecommerce-msa/
├── settings.gradle
├── build.gradle
├── order-service/
├── payment-service/
├── inventory-service/
├── notification-service/
└── docker-compose.yml
```

### 기술 스택

- **Spring Boot** (각 마이크로서비스)
- **Spring Kafka**
- **Docker Compose** (로컬 환경 구성)
- **PostgreSQL** (각 서비스별 독립 DB)
