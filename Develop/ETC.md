# 실개발 시 알게된 것들

## 에러 처리
- ControllerAdvice를 통해 글로벌 에러를 처리할 때, 어떤 Exception에서 어떤 에러를 처리하는지 정리
```java
@Slf4j
@ControllerAdvice
@RequiredArgsConstructor
public class GlobalExceptionHandler {
    // 최상위 Exception을 처리 여기서 특정에러는 넘길지 통제 할 수 있다.
    @ExceptionHandler(Exception.class)
    public ResponseEntity<?> handleGeneralException(Exception e) throws Exception {
    
    }

    // MethodArgumentNotValidException
    // @RequestBody로 바인딩된 객체 DTO에 대해 Bean Validation(JSR-380) 어노테이션(@NotNull, @Size, @Email …) 검증이 실패했을 때
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<?> handlerArgumentNotValidException(MethodArgumentNotValidException e) {

    }

    // HandlerMethodValidationException
    // @RequestParam, @PathVariable, @ModelAttribute 같은 메서드 파라미터 단위에 Bean Validation을 적용했을 때 검증 실패 시
    @ExceptionHandler(HandlerMethodValidationException.class)
    public ResponseEntity<?> handlerMethodValidationException(HandlerMethodValidationException e) {
        
    }

    // MissingServletRequestParameterException
    // @RequestParam인데 파라미터가 아예 없을 경우 발생
    // Bean Validation 이전에 아니라 HandlerMethodArgumentResolver가 담당하는 로직에서 발생하는 에러를 처리
    // 컨트롤러에서 @RequestParam이 **필수(required=true, 기본값)**인데 요청 파라미터가 아예 누락되었을 때
    @ExceptionHandler(MissingServletRequestParameterException.class)
    public ResponseEntity<?> handlerMissingParams(MissingServletRequestParameterException e) {
        
    }

    // HttpMessageNotReadableException
    // 클라이언트에서 전송하는 Json/HTTP body를 Jackson이 DTO로 변환할 수 없을 때 발생
    // 타입이 달라서 발생하는 것도 여기서 잡힘
    // 요청 본문(body)을 HttpMessageConverter가 객체로 변환하는 단계에서 JSON/XML 등 파싱 자체가 실패할 때
    // 대표 상황
    // JSON 문법 오류
    // DTO 필드 타입 불일치 (문자열 → 숫자 변환 실패 등)
    @ExceptionHandler({HttpMessageNotReadableException.class})
    public ResponseEntity<?> handlerHttpMessageNotReadable(HttpMessageNotReadableException e) {

    }

    // ConstraintViolationException
    // 메서드/ 서비스 계층에서 파리미터 검증이 실패 했을 때 발생
    // @RequstBody도 여기일 것 같지만 아님, 애초에 Body와 파라미터가 변환되는 방식 자체가 다름
    // 여기서는 Body 빼고 받는 모든 것들이 제대로 안왔을때 여기에 걸린다.
    @ExceptionHandler(ConstraintViolationException.class)
    public ResponseEntity<?> handleConstraintViolationException(ConstraintViolationException e, HttpServletRequest request) {
        
    }
```

## 엔티티 작성시 ENUM 다루기

1. DB 중심 설계
```java
    @Column(columnDefinition = "ENUM('싱글', '연애 중', '기혼')")
    private String relationshipStatus;
```
- DB의 ENUM 타입을 직접 사용하고 명시한 것이다.
- 검증 책임이 DB 자체에 있다.
- ENUM 값이 바뀌게 되면 DB DLL 자체를 수정해야한다.
- 확장성이 떨어진다.

2. 도메인 중심 설계
```java
    // enum 선언
    @AllArgsConstructor
    @Getter
    public enum RelrationShipStatus {
        SIGLE("싱글"),
        DATING("연애 중"),
        MARRIED("기혼");
    
        private final String code;
    }

    // 컨버터 정의
    @Converter(autoApply = true)
    public class RelrationShipStatusConverter extends BaseEnumConverter<RelrationShipStatus> {
        public RelrationShipStatusConverter() {
            super(RelrationShipStatus.class);
        }
    }

    // 엔티티에서 사용
    @Convert(converter = RelrationShipStatusConverter.class) 
    @Column(nullable = false) 
    private RelrationShipStatus relrationShipStatus;
```
- 자바의 enum 타입 사용
- 자바 코드 에서 타입 안정성이 보장된다.(자바에 검증 책임이 있다.)
- Enum 수정만 회면 된다 DB는 그냥 vachar로 된다.
- 확장성이 크다.

## 이벤트 처리
어떤 일이 발생 했을 때, 그일을 구독하고 있는 객체가 반응하도록 하는 설계 방식 즉, “A 서비스에서 무언가 일이 일어나면 B 서비스가 반응해야 한다”를 직접 의존 호출 대신 이벤트로 느슨하게 연결하는 방식
- 스프링 이벤트 구조
    - 이벤트 발행자 (publisher): ApplicationEventPublisher.publishEvent(event) 로 이벤트를 발생시킴
    - 이벤트 객체 (Event): 보통 record 또는 간단한 POJO로 정의
    - 이벤트 리스너 (Listener): @EventListener 어노테이션이 붙은 메서드에서 이벤트를 처리
- 장점
    1. 의존성 감소 :  서비스간 진접적인 호출이 없어서, 모듈 간 결합도를 낮출 수 있음
    2. 단일 책임 원칙 강화 : 한 서비스는 자신의 핵심 로직만 처리하고, 부수효과는 다른 리스너에 위임
    3. 확장성 : 새로운 후처리를 추가할 때 기존 서비스 수정 없이 새로운 리스너만 추가 가능
    4. 비동기 처리 가능 :  @Async를 붙이면 비즈니스 로직과 별개로 비동기로 동작
    5. 트랜잭션 이벤트 처리 가능 : @TransactionalEventListener를 사용해 커밋 후 실행 등 제어 가능
- 단점
    1. 흐름 추적이 어려움 : 서비스 내에서 어떤 이벤트가 어디로 흘러가는지 파악하기 어려움
    2. 명시적 의존성 사라짐 :  리스너가 자동 실행되기 때문에, 코드상에 호출 흔적이 없어 숨은 로직이 생길 수 있음
    3. 남용시 유지 보수 어려움 : 단순히 메서드 호출로 해결 가능한 것을 이벤트로 만들면 오히려 복잡해짐
    4. 에러 전파 제어 어려옴 :  비동기 이벤트일 경우 예외가 퍼지지 않아 실패 감지가 어려움
- 사용 판단 기준
    - 핵심 로직과 부수적 관심사를 분리하고 싶을 때 또는 트랜잭션 완료 이후에 후처리를 하고 싶을 때
    - ex
        - 회원 가입 후 후처리 : 이메일/SMS 발송, 가입 로그, 추천인 포인트 지급 등
        - 결제 완료 후 후처리 : 영수증 발급, 마일리지 적립, 결제 이력 저장
        - 감사 로그 / 이력 기록 : 서비스 로직에서 감사로 그 기록을 분리
        - 비동기 처리 필요 : 알림, 통계 집계, Slack 알림 등 실시간성 필요 없는 작업
        - 다양한 리스너가 반응해야 할 때, 하나의 이벤트에 여러 리스너가 각기 다른 후처리를 하는 경우
```java
// 이벤트
    public record AuditLogEvent(String action, Long userId) {}
    
    // 발행
    eventPublisher.publishEvent(new AuditLogEvent("USER_REGISTER", user.getId()));
    
    // 리스너
    @Component
    public class AuditLogListener {
        @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
        public void handle(AuditLogEvent event) {
            auditLogService.record(event.action(), event.userId());
        }
    }
```

