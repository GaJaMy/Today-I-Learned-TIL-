# 실개발 시 알게된 것들


## 공통 처리
## 응답 처리
```java
    @Getter
    @Builder
    @NoArgsConstructor
    @AllArgsConstructor
    public class ResponseDto<T> {
        @Schema(description = "에러 코드", example = "SU000")
        private String errorCode;
        @Schema(description = "응답 메시지", example = "ok")
        private String msg;
        @Setter
        @Schema(description = "응답 데이터")
        private T data;
    }
```

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
- Enum 수정만 하면 된다 DB는 그냥 varchar로 된다.
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



# Mockito
## 기본 용어 정리
- Mock: 테스트 대상의 외부 의존성을 흉내내는 가짜 객체. (@Mock)
- Stub / Stubbing: mock에 특정 입력에 대해 정해진 동작(리턴값/예외)을 미리 설정하는 것. when(...).thenReturn(...) 같은 구문이 스터빙.
- Spy: 실제 객체의 일부는 사용하고 일부 메서드만 가로채는 형태. (@Spy)
- Verify (행위 검증): 특정 mock 메서드가 호출되었는지 검증 (verify(...)).
- ArgumentCaptor: mock으로 전달된 인자를 캡처해서 내부 값을 검사.
- BDD style: BDDMockito.given(...).willReturn(...) — 가독성을 위해 사용.



## 자주 쓰는 Mockito API (분류별)
1. 스터빙 (값/동작 지정)
    - when(mock.method(args)).thenReturn(value)
    - when(mock.method(args)).thenThrow(exception)
    - when(mock.method(args)).thenAnswer(answer) — 동적 응답, 호출 시점의 인자로 로직 결정
    - when(mock.method(args)).thenCallRealMethod() — 실제 구현 호출
- BDD 대체:
    - given(mock.method(args)).willReturn(value)


- 예:
    ```java
        when(userAdaptor.getUser("id")).thenReturn(Optional.of(user));
        when(service.calc(1)).thenReturn(100);
        when(service.calc(2)).thenThrow(new IllegalStateException());
        when(service.echo(anyString())).thenAnswer(invocation -> "echo:" + invocation.getArgument(0));
    ```



2. void 메서드 / Spy용 대체 스터빙
    - doReturn(value).when(spy).method(args) — spy에서 사용 (또는 final/private 등으로 when() 사용 불가 시)
    - doThrow(exception).when(mock).voidMethod(args) — void 메서드 예외 스터빙
    - doNothing().when(mock).voidMethod(args) — 기본(no-op) 설정
    - doAnswer(answer).when(mock).voidMethod(args)
- 예:
    ```java
        doThrow(new RuntimeException()).when(mockedDao).deleteById(1L);
        doReturn(42).when(spyObj).calculate(1);
    ```
    이유: when(spy.method()).thenReturn(...) 는 spy의 실제 메서드가 먼저 실행될 수 있어서 안전하지 않음. 그래서 doReturn 계열을 사용.


3. 행위 검증 (verify)
    - verify(mock).someMethod(args) — 호출 여부 기본 검증
    - verify(mock, times(n)).someMethod(args) — 호출 횟수
    - verify(mock, never()).someMethod(...) — 호출되지 않았음
    - verify(mock, atLeast(n)).someMethod(...)
    - verify(mock, atMost(n)).someMethod(...)
    - verifyNoMoreInteractions(mock) — 더 이상 다른 호출 없음
    - inOrder(mock1, mock2) → inOrder.verify(mock1).a(); inOrder.verify(mock2).b(); 순서 검증
- 예:
    ```java
        verify(userAdaptor, times(1)).getUser(userId);
        verify(cache, never()).put(any());
        InOrder inOrder = inOrder(repo, notifier);
        inOrder.verify(repo).save(entity);
        inOrder.verify(notifier).notify(entity);
    ```

4. Argument Matcher (인자 매처)
    - any(), anyString(), anyLong(), eq(value) 등
    - 주의: 매처를 쓰면 같은 메서드 호출에서 모든 인자에 매처를 사용하거나 eq(...)와 조합해야 함. (혼합 시엔 eq()를 사용)
- 예:
    ```java
        when(repo.findByName(anyString())).thenReturn(user);
        verify(repo).save(eq(expectedUser));
    ```

5. ArgumentCaptor (인자 캡처)
    - @Captor ArgumentCaptor<MyDto> captor;
    - verify(mock).save(captor.capture());
    - captor.getValue()로 내부 필드 검증
- 예:
    ```java
        ArgumentCaptor<User> captor = ArgumentCaptor.forClass(User.class);
        verify(userRepo).save(captor.capture());
        assertEquals("name", captor.getValue().getName());
    ```

6. Spy, Partial Mocking


@Spy 또는 spy(new RealObj())


일부 메서드만 mocking하고 나머지는 실제 동작 사용


예:
MyService spy = spy(new MyService(realRepo));
doReturn(5).when(spy).compute(anyInt());


G. 정적 메서드 / final / 생성자 mocking (고급)


mockStatic(SomeClass.class) (Mockito-inline 또는 Mockito 3.4+ 필요)


mockConstruction 등도 존재 (라이프사이클 제어)



H. Mockito 어노테이션들


@Mock — mock 생성


@Spy — spy 생성


@InjectMocks — mock을 대상 객체에 주입


@Captor — ArgumentCaptor 생성


@ExtendWith(MockitoExtension.class) — JUnit5 통합



3) 언제 뭘 써야 하는가 (권장 패턴)


외부 의존성(DB, repo, REST client 등) → @Mock + when(...).thenReturn(...) 로 동작 고정.


void 메서드에서 예외를 발생시키려면 doThrow(...) 사용.


spy로 실제 구현을 사용하려면 doReturn(...) 방식으로 안전하게 스텁.


반환값의 동작이 입력 인자에 따라 달라진다면 thenAnswer(invocation -> { ... }) 사용.


행위 검증(부수 효과 검사)이 목적이면 verify(...) 사용.


결과값(assertion) 중심이면 assertEquals/AssertJ로 상태 검증.



4) 실전 예제 모아보기
@ExtendWith(MockitoExtension.class)
class ExampleTest {
    @Mock
    Repo repo;

    @InjectMocks
    MyService service;

    @Captor
    ArgumentCaptor<User> userCaptor;

    @Test
    void stubbing_example() {
        User u = new User("id", "name");
        when(repo.findById("id")).thenReturn(Optional.of(u));

        User result = service.getUser("id");
        assertEquals("name", result.getName());
        verify(repo, times(1)).findById("id");
    }

    @Test
    void void_and_doThrow_example() {
        doThrow(new RuntimeException("fail")).when(repo).delete("bad-id");
        assertThrows(RuntimeException.class, () -> service.deleteUser("bad-id"));
        verify(repo).delete("bad-id");
    }

    @Test
    void spy_example() {
        MyService real = new MyService(repo);
        MyService spy = spy(real);
        doReturn(99).when(spy).calculate(anyInt());

        int v = spy.calculate(1);
        assertEquals(99, v);
    }

    @Test
    void answer_example() {
        when(repo.save(any(User.class))).thenAnswer(inv -> {
            User arg = inv.getArgument(0);
            arg.setId("generatedId");
            return arg;
        });

        User u = new User(null, "name");
        User saved = service.register(u);
        assertNotNull(saved.getId());
    }

    @Test
    void captor_example() {
        User u = new User("id","n");
        service.register(u);
        verify(repo).save(userCaptor.capture());
        assertEquals("n", userCaptor.getValue().getName());
    }
}


5) 흔한 실수 & 팁


Spy + when(...) 사용하면 실제 메서드가 실행될 수 있음 → doReturn 권장.


ArgumentMatchers와 실제 값 혼용 시 타입 불일치 에러 발생 → eq()와 조합하거나 모든 인자에 매처 사용.


verifyNoMoreInteractions()는 디버깅엔 유용하지만 유지보수에서 불편함을 줄 수 있으니 신중하게.


여러 번 when(...).thenReturn(...) 하면 마지막 설정이 유효. 복수 호출 상황은 thenReturn(a, b, c) 사용 가능.


불필요한 reset(mock)은 피하라 — 테스트를 이해하기 어렵게 만듦.



6) 간단한 요약(치트시트)


값 반환 스터빙: when(...).thenReturn(...)


예외 스터빙: when(...).thenThrow(...) / doThrow(...).when(...)(void)


동적 응답: thenAnswer(...)


spy 안전 스터빙: doReturn(...).when(spy)...


행위 검증: verify(mock) + times(), never(), inOrder(...)


인자 매칭: any(), anyString(), eq(...)


인자 캡처: ArgumentCaptor


BDD 스타일: given(...).willReturn(...)



필요하면, 지금 TokenService 테스트나 UserService 테스트에 위 패턴을 적용해서 구체적인 테스트 코드(예: doThrow로 void 예외 스텁, thenAnswer로 동적 값 반환, ArgumentCaptor로 내부 DTO 검증 등)를 바로 만들어 드릴게요. 어느 메서드/시나리오에 적용해볼까요?