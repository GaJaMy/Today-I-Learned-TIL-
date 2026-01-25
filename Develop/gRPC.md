# gRPC 핵심 가이드

## 1. gRPC란?

Google이 개발한 고성능 RPC 프레임워크
- **HTTP/2 + Protocol Buffers** 사용
- 바이너리 프로토콜로 빠른 성능
- 다중 언어 지원 (Java, Python, Go 등)
- 완전한 구현체 (JPA처럼 인터페이스가 아님)

---

## 2. 장단점

### 장점 ✅

**1. 뛰어난 성능**
- JSON 대비 3~10배 작은 데이터 크기
- HTTP/2 멀티플렉싱: 단일 연결로 다중 요청 동시 처리
- 헤더 압축으로 대역폭 절약

**대용량 처리 예시**
```
1000명 통계 조회

REST (HTTP/1.1): 1000번 연결, 순차 처리 → 10초
gRPC (HTTP/2): 1번 연결, 병렬 처리 → 2초
```

**2. 타입 안정성**
- 컴파일 타임에 타입 체크
- proto 파일이 명확한 계약서 역할

**3. 양방향 스트리밍**
```protobuf
service ChatService {
  rpc Chat(stream Message) returns (stream Message) {}
}
```

### 단점 ❌
- 브라우저 지원 제한 (gRPC-Web 필요)
- 바이너리 데이터라 디버깅 어려움
- REST보다 높은 학습 곡선

### 사용 케이스
- **gRPC 추천**: 마이크로서비스 간 통신, 대용량 처리, 실시간 스트리밍
- **REST 추천**: 웹 브라우저 클라이언트, 공개 API, 간단한 CRUD

---

## 3. 핵심 개념

### protoc 동작 방식
```
.proto 파일 작성 (개발자)
    ↓
protoc 컴파일 (빌드 시점)
    ↓
Java 코드 자동 생성
    ↓
개발자가 비즈니스 로직 구현
```

### 4가지 통신 패턴
```protobuf
service ExampleService {
  // 1. Unary: 단일 요청 → 단일 응답
  rpc GetUser(UserRequest) returns (UserResponse) {}
  
  // 2. Server Streaming: 단일 요청 → 여러 응답
  rpc ListUsers(Request) returns (stream UserResponse) {}
  
  // 3. Client Streaming: 여러 요청 → 단일 응답
  rpc Upload(stream Request) returns (Response) {}
  
  // 4. Bidirectional: 양방향 스트림
  rpc Chat(stream Message) returns (stream Message) {}
}
```

---

## 4. Protocol Buffers 필수 문법

### 기본 구조
```protobuf
syntax = "proto3";

package chatbot;
option java_package = "com.example.grpc.chatbot";

service ChatbotService {
  rpc GetStats(StatsRequest) returns (StatsResponse) {}
}

message StatsRequest {
  repeated string user_ids = 1;  // List<String>
  string platform_id = 2;        // String
}

message StatsResponse {
  repeated UserStats stats = 1;
}

message UserStats {
  string user_id = 1;
  int64 chat_count = 2;
  repeated string keywords = 3;
}
```

### 데이터 타입
```protobuf
int32 → int
int64 → long
string → String
bool → boolean
repeated → List<>
map<string, int32> → Map<String, Integer>
```

### 필드 번호 규칙 ⚠️
```protobuf
message User {
  string name = 1;   // 1~15: 자주 사용
  int32 age = 2;
  
  reserved 3;        // 삭제된 번호는 reserved
  string email = 4;  // 새 필드는 새 번호
}
```
- **한번 사용한 번호는 절대 재사용 금지** (하위 호환성)

---

## 5. Spring Boot 설정

### build.gradle
```gradle
plugins {
    id 'com.google.protobuf' version '0.9.4'
}

dependencies {
    implementation 'net.devh:grpc-spring-boot-starter:2.15.0.RELEASE'
    implementation 'io.grpc:grpc-protobuf:1.58.0'
    implementation 'io.grpc:grpc-stub:1.58.0'
}

protobuf {
    protoc {
        artifact = "com.google.protobuf:protoc:3.24.0"
    }
    plugins {
        grpc {
            artifact = 'io.grpc:protoc-gen-grpc-java:1.58.0'
        }
    }
    generateProtoTasks {
        all()*.plugins { grpc {} }
    }
}
```

### 프로젝트 구조
```
src/main/
├── proto/                    ⭐ proto 파일
│   └── service.proto
├── java/
│   ├── grpc/                ⭐ gRPC 서비스 구현
│   ├── service/             ⭐ 비즈니스 로직
│   └── repository/          ⭐ DB 접근
└── resources/
    └── application.yml
```

### application.yml
```yaml
# 서버
grpc:
  server:
    port: 9090

# 클라이언트
grpc:
  client:
    chatbot-service:
      address: 'static://localhost:9090'
      negotiationType: plaintext
```

---

## 6. 서버 구현

### 간단한 구현
```java
@GrpcService
public class ChatbotServiceImpl 
        extends ChatbotServiceGrpc.ChatbotServiceImplBase {
    
    @Autowired
    private ChatRepository repository;
    
    @Override
    public void getStats(StatsRequest request, 
            StreamObserver<StatsResponse> responseObserver) {
        
        try {
            // 1. 요청 데이터 추출
            List<String> userIds = request.getUserIdsList();
            
            // 2. 비즈니스 로직
            List<UserStats> stats = userIds.stream()
                .map(id -> calculateStats(id))
                .collect(Collectors.toList());
            
            // 3. 응답 생성
            StatsResponse response = StatsResponse.newBuilder()
                .addAllStats(stats)
                .build();
            
            // 4. 응답 전송
            responseObserver.onNext(response);
            responseObserver.onCompleted();
            
        } catch (Exception e) {
            responseObserver.onError(
                Status.INTERNAL.withDescription(e.getMessage())
                    .asRuntimeException()
            );
        }
    }
}
```

### Service 분리 (복잡한 경우)
```java
// gRPC 서비스 - 얇게 유지
@GrpcService
public class ChatbotServiceImpl 
        extends ChatbotServiceGrpc.ChatbotServiceImplBase {
    
    @Autowired
    private StatsService statsService;  // 비즈니스 로직 분리
    
    @Override
    public void getStats(StatsRequest request, 
            StreamObserver<StatsResponse> responseObserver) {
        
        List<UserStatsDto> stats = statsService.getStats(
            request.getUserIdsList());
        
        StatsResponse response = convertToGrpc(stats);
        responseObserver.onNext(response);
        responseObserver.onCompleted();
    }
}

// 비즈니스 로직 - REST와 공유 가능
@Service
public class StatsService {
    public List<UserStatsDto> getStats(List<String> userIds) {
        // DB 조회 및 비즈니스 로직
    }
}
```

---

## 7. 클라이언트 구현

```java
@Service
public class ChatbotClient {
    
    @GrpcClient("chatbot-service")
    private ChatbotServiceGrpc.ChatbotServiceBlockingStub stub;
    
    public StatsResponse getStats(List<String> userIds) {
        
        StatsRequest request = StatsRequest.newBuilder()
            .addAllUserIds(userIds)
            .build();
        
        return stub.getStats(request);
    }
}
```

### REST Controller에서 gRPC 호출
```java
@RestController
@RequestMapping("/api/stats")
public class StatsController {
    
    @Autowired
    private ChatbotClient chatbotClient;
    
    @PostMapping
    public ResponseEntity<StatsDto> getStats(@RequestBody StatsRequestDto dto) {
        
        StatsResponse grpcResponse = chatbotClient.getStats(dto.getUserIds());
        
        return ResponseEntity.ok(convertToDto(grpcResponse));
    }
}
```

---

## 8. proto 파일 공유 ⚠️ 중요

**양쪽 서버가 반드시 같은 proto 파일을 공유해야 함**

### Git Submodule 방식 (추천)
```bash
# 1. proto 전용 저장소 생성
grpc-contracts/
└── src/main/proto/
    └── service.proto

# 2. 각 프로젝트에서 submodule 추가
cd chatbot-server
git submodule add https://github.com/company/grpc-contracts.git proto

cd platform-server
git submodule add https://github.com/company/grpc-contracts.git proto
```

### build.gradle
```gradle
sourceSets {
    main {
        proto {
            srcDir 'proto/src/main/proto'  // submodule 경로
        }
    }
}
```

### proto 변경 워크플로우
```bash
# 1. proto 수정 및 태깅
cd grpc-contracts
vim service.proto
git commit -m "feat: add field"
git tag v1.1.0
git push origin v1.1.0

# 2. 서버 업데이트 (먼저!)
cd chatbot-server
git submodule update --remote proto
./gradlew generateProto

# 3. 클라이언트 업데이트
cd platform-server
git submodule update --remote proto
./gradlew generateProto
```

---

## 9. 아키텍처 비교

### REST
```
@RestController (URL 엔드포인트 정의)
    ↓
@Service (비즈니스 로직)
    ↓
@Repository (DB)
```

### gRPC
```
@GrpcService (proto가 엔드포인트 정의)
    ↓
@Service (선택적, 복잡한 로직 분리)
    ↓
@Repository (DB)
```

**핵심**: proto 파일이 REST의 @RequestMapping 역할을 대신함

---

## 10. 핵심 정리

### gRPC 선택 기준
- ✅ 마이크로서비스 간 내부 통신
- ✅ 대용량 데이터 (HTTP/2 멀티플렉싱)
- ✅ 높은 성능 필요 (바이너리 프로토콜)
- ❌ 웹 브라우저 직접 통신
- ❌ 공개 API

### 대용량 처리 우수성
1. 바이너리 → JSON 대비 3~10배 작음
2. HTTP/2 → 단일 연결로 다중 요청 병렬 처리
3. 헤더 압축 → 대역폭 절약

### 필수 체크리스트
- [ ] proto 파일 양쪽 공유 (Git Submodule)
- [ ] 필드 번호 재사용 금지 (reserved 사용)
- [ ] 서버 먼저 배포 → 클라이언트 업데이트
- [ ] 에러 처리 (Status 코드 사용)
- [ ] 타임아웃 설정

### 개발 순서
1. proto 파일 작성
2. `./gradlew generateProto`
3. 서버 구현 (`@GrpcService`)
4. 클라이언트 구현 (`@GrpcClient`)
5. 테스트