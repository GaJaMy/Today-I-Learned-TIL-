# 클린 아키텍쳐
로버트 C. 마틴이 제안한 소프트웨어 설계 원칙으로, 관심사 분리를 통해 시스템을 유연하고, 유지보수하기 쉬우며, 테스트하기 용이하게 만드는 것을 목표로 한다. 여러 계층으로 시스템을 나누고 계층 간의 의존성을 최소화하며, 핵심 비즈니스 로직을 외부 기술에 종속되지 않도록 설계하는 것이 특징

## 의존 규칙
클린 아키텍쳐의 의존 규칙은 안쪽을 향해야 한다. 안쪽의 원은 바깥쪽 원에 대해 알 수 없어야 한다.

![클린 아키텍쳐 이미지](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdna%2FEeyCW%2Fbtq4oQ8NtZa%2FAAAAAAAAAAAAAAAAAAAAAKjuYsEkXUhU1MIRWKJZ7CQHonbwLW2KXfxCEPIpE-lm%2Fimg.jpg%3Fcredential%3DyqXZFxpELC7KVnFOS48ylbz2pIh7yKj8%26expires%3D1759244399%26allow_ip%3D%26allow_referer%3D%26signature%3DDYxp%252BqP0AVZKnsxyq1QSw5L%252Bz3E%253D)

## 레이어 구조

### Entities (엔티티 계층, 도메인 모델)
- 비즈니스 규칙의 핵심으로, 시스템에서 가장 중요한 개념 정의
- 데이터와 행동을 함께 가지는 도메인 객체(예: Order, User, Account)
- 외부 세계와 완전히 독립적 -> 데이터 베이스 , UI, 프레임 워크를 몰라도 됨

### Use Cases (유스케이스 계층, 애플리케이션 계층)
- 애플리케이션의 비즈니스 로직을 정의
- 외부 입출력이나 기술적 세부사항을 몰라도 됨
- 엔티티를 조합하고, 트랜잭션 단위를 정의

### Interface Adapters (인터페이스 어댑터 계층)
- 외부 세계(웹, DB, 메시지 브로커 등)와 애플리케이션 계층을 연결
- DTO, Mapper, Repository 구현체, Controller와 같은 요소들이 포함
- 유즈케이스가 요구하는 형식과 외부 세계가 사용하는 형식 간 변환 담당

### Frameworks & Drivers (프레임워크/드라이버 계층)
- 데이터베이스, 웹 프레임워크, 메시징 시스템, 외부 API
- 기술적인 세부사항 담당 (JPA, Kafka, HTTP Server 등)
- 변경 가능성이 가장 크기 때문에 의존성이 안쪽으로 들어가지 안혿록 주의
