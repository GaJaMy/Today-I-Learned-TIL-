# 스프링 서버 개발자를 위한 핵심 인프라 지식

## 1. Tomcat (톰캣)

### 무엇인가?
- 스프링 부트의 **기본 내장 웹 서버**
- 서블릿 컨테이너 (Servlet Container)
- HTTP 요청을 받아서 자바 코드로 전달하는 역할

### 왜 중요한가?
```
클라이언트 HTTP 요청 → Tomcat → DispatcherServlet → Controller
```

### 핵심 개념
- **스레드 풀**: 요청마다 스레드를 할당 (기본 200개)
- **포트**: 기본 8080 포트로 HTTP 요청 수신
- **설정 예시**:
```yaml
server:
  port: 8080
  tomcat:
    threads:
      max: 200        # 최대 스레드 수
      min-spare: 10   # 최소 대기 스레드
    max-connections: 8192  # 최대 동시 연결 수
```

### 실무 포인트
- 요청 1개 = 스레드 1개 사용
- 스레드가 부족하면 요청이 대기 상태로
- 느린 API가 있으면 스레드 고갈 위험

---

## 2. Connection Pool (커넥션 풀)

### 무엇인가?
- **DB 연결을 미리 만들어두고 재사용**하는 기술
- 매번 DB 연결 생성/해제는 비용이 크기 때문

### 작동 방식
```
[애플리케이션]
    ↓ 연결 요청
[커넥션 풀] - [연결1] [연결2] [연결3] ... [연결10]
    ↓
[데이터베이스]
```

### 대표 구현체

#### HikariCP (히카리)
- 스프링 부트 **기본 커넥션 풀**
- 가장 빠르고 경량화

```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 10      # 최대 커넥션 수
      minimum-idle: 5            # 최소 유휴 커넥션
      connection-timeout: 30000  # 연결 대기 시간 (ms)
      idle-timeout: 600000       # 유휴 커넥션 유지 시간
```

### 실무 포인트
- **너무 많은 커넥션**: DB 부하 증가
- **너무 적은 커넥션**: 대기 시간 증가
- 일반적으로 **10~20개**면 충분 (트래픽에 따라 조정)
- 커넥션 부족 시 `Connection timeout` 에러 발생

### 주의사항
```java
// ❌ 나쁜 예: 커넥션 누수
public void badExample() {
    Connection conn = dataSource.getConnection();
    // ... 작업 후 close() 안 함
}

// ✅ 좋은 예: try-with-resources
public void goodExample() {
    try (Connection conn = dataSource.getConnection()) {
        // ... 작업
    } // 자동으로 close()
}
```

---

## 3. Netty (네티)

### 무엇인가?
- **비동기 논블로킹 네트워크 프레임워크**
- Tomcat과 다른 방식으로 요청 처리
- Spring WebFlux의 기본 서버

### Tomcat vs Netty 비교

#### Tomcat (전통적 방식)
```
요청 1 → 스레드 1 (블로킹, 완료될 때까지 대기)
요청 2 → 스레드 2
요청 3 → 스레드 3
```
- 스레드가 I/O 작업 중 대기 (블로킹)
- 많은 동시 요청 시 스레드 부족 가능

#### Netty (비동기 방식)
```
요청 1 ─┐
요청 2 ─┼→ 소수의 스레드가 이벤트 루프로 처리
요청 3 ─┘
```
- 스레드가 I/O 대기 없이 다른 작업 처리
- 적은 스레드로 많은 요청 처리 가능

### 언제 사용하나?
- **Tomcat 사용**: 일반적인 REST API, MVC 패턴
- **Netty 사용**: 
  - 많은 동시 연결 (채팅, 스트리밍)
  - WebFlux 사용 시
  - 비동기 Non-Blocking I/O가 필요한 경우

```yaml
# WebFlux 사용 시 (Netty가 기본)
spring:
  webflux:
    base-path: /api
```

### 실무 포인트
- 일반적인 Spring MVC 프로젝트는 **Tomcat으로 충분**
- 특별한 요구사항 없으면 Netty로 전환할 필요 없음
- WebFlux는 러닝커브가 있으므로 신중히 선택

---

## 4. Thread Pool (스레드 풀)

### 무엇인가?
- 미리 생성된 스레드들을 재사용하는 구조
- 스레드 생성/제거 비용 절감

### 주요 스레드 풀

#### 1) Tomcat 스레드 풀
```yaml
server:
  tomcat:
    threads:
      max: 200  # HTTP 요청 처리용
```

#### 2) 비동기 작업 스레드 풀
```java
@Configuration
@EnableAsync
public class AsyncConfig {
    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);      // 기본 스레드 수
        executor.setMaxPoolSize(10);      // 최대 스레드 수
        executor.setQueueCapacity(100);   // 대기 큐 크기
        executor.setThreadNamePrefix("async-");
        return executor;
    }
}
```

### 실무 포인트
```java
// 비동기 메서드
@Async
public CompletableFuture<String> asyncTask() {
    // 별도 스레드에서 실행
    return CompletableFuture.completedFuture("완료");
}
```

---

## 5. HTTP/HTTPS와 SSL/TLS

### HTTP vs HTTPS
- **HTTP**: 평문 통신 (보안 취약)
- **HTTPS**: SSL/TLS로 암호화된 통신

### SSL/TLS 설정 (스프링 부트)
```yaml
server:
  port: 8443
  ssl:
    enabled: true
    key-store: classpath:keystore.p12
    key-store-password: password
    key-store-type: PKCS12
```

### 실무에서
- 개발환경: HTTP (8080)
- 운영환경: HTTPS (443) - 보통 Nginx/AWS ALB에서 처리

---

## 6. 실전 모니터링 지표

### 꼭 확인해야 할 것들

#### 1) 스레드 상태
```java
// Actuator 활성화
management:
  endpoints:
    web:
      exposure:
        include: health,metrics,threaddump

// 확인: GET /actuator/metrics/tomcat.threads.busy
```

#### 2) 커넥션 풀 상태
```java
// GET /actuator/metrics/hikaricp.connections.active
// GET /actuator/metrics/hikaricp.connections.idle
```

#### 3) 응답 시간
```java
// GET /actuator/metrics/http.server.requests
```

---

## 7. 흔한 문제와 해결

### 문제 1: "Connection timeout"
**원인**: 커넥션 풀 부족
**해결**: 
```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 20  # 증가
```

### 문제 2: "Too many open files"
**원인**: 동시 연결 수 제한
**해결**:
```yaml
server:
  tomcat:
    max-connections: 10000  # 증가
```

### 문제 3: 느린 응답
**원인**: 
- 느린 쿼리
- 외부 API 호출 블로킹
- 스레드 고갈

**해결**:
- 쿼리 최적화
- 외부 API는 비동기 처리
- 타임아웃 설정
```yaml
spring:
  datasource:
    hikari:
      connection-timeout: 3000  # 3초
```

---

## 8. 권장 설정 (중소규모 서비스)

```yaml
server:
  port: 8080
  tomcat:
    threads:
      max: 200
      min-spare: 10
    max-connections: 8192
    connection-timeout: 20000

spring:
  datasource:
    hikari:
      maximum-pool-size: 10
      minimum-idle: 5
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
```

---

## 요약

| 항목 | 역할 | 기본값 | 조정 시점 |
|------|------|--------|----------|
| **Tomcat 스레드** | HTTP 요청 처리 | 200개 | 응답 지연 발생 시 |
| **커넥션 풀** | DB 연결 관리 | 10개 | Connection timeout 시 |
| **Netty** | 비동기 서버 | - | 많은 동시 연결 필요 시 |
| **비동기 스레드** | 백그라운드 작업 | - | 무거운 작업 분리 시 |

### 초보자 체크리스트
- [ ] Tomcat이 HTTP 요청을 받는다는 것 이해
- [ ] 커넥션 풀이 왜 필요한지 이해
- [ ] 기본 설정값으로 시작해도 괜찮다는 것 인지
- [ ] 문제 발생 시 어떤 지표를 봐야 하는지 파악
- [ ] Actuator로 모니터링하는 방법 습득

**핵심**: 처음부터 완벽한 설정은 불필요합니다. 기본 설정으로 시작하고, 문제가 생기면 모니터링 지표를 보며 튜닝하세요!
