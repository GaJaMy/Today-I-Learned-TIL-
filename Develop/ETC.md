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
