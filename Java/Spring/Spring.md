1. SOLID 원칙
    + SRP : 단일 책임 원칙
        - 한 클래스는 단일의 책임만 가져야 한다.
    + OCP : 개방 폐쇄 원칙
        - 확장에는 열려있고, 변경에는 닫혀있다.
    + LSP : 리스코프 치환 법칙
        - 서브타입은 언제나 기반타입으로 교체할 수 있어야 한다.
        - 즉, 상속받은 클래스는 부모 클래스와 동일한 동작을 해야 재활용 가능성이 높아진다.
        - 상속의 대안 또는 상속을 잘하는 방법
            - 상속을 위한 설계를 한 클래스만 상속하라
            - 부모 클래스 상속 대신 인터페이스를 활용하라
            - 피할 수 없다면 상속을 하지만 부모와 상호 치환이 가능 하도록 하라
    + ISP : 인터페이스 분리 원칙
        - 인터페이스도 단일 책임을 갖도록 분리해야 한다.
            - SRP와 다소 유사하지만 인터페이스도 단일의 책임을 갖도록 설계해야 필요한 기능만 구현하고 제공할 수 있다.
            - 너무 큰 인터페이스를 만들면 빈 메서드를 만드는 경우가 발생
    + DIP : 의존성 역전 원칙
        - 하위 모듈의 변경이 상위 모듈의 변경을 요구하는 의존성을 끊어 내야한다.
            - 개발을 하다보면 내가 사용하던 라이브러리를 다른 라이브러리로 변경하면 코드를 다 뜯어 고쳐야하는 경우가 있는데 그렇게 라이블러리에 직접적으로 의존하면 교체가 어렵다.

2. Spring 프로젝트 만들기
    1. Start.Spring.io 에서 만듬
    2. 직접 만들기
        + 새 프로젝트 -> Spring initializer
            - 이름, 위치 설정
            + 언어 -Java
            + 타입 -Maven, gradle 선택
            + SDK 선택
            + Java 버전 선택
            + 패키지 종류 선택
            + next
        + 스프링 부트 버전 선택
            + Spring Boot - 알맞는 것으로 선택
            + 추후 아래에서 필요한것들 선택 후 create

3. 스프링 기술
    + DI,IoC,컨테이너 : 스프링의 근간을 이루고 전체 어플리케이션을 통제하는데 활용되는 기술
    + Resource,AOP,Validation,SpEl : 많은 어플리케이션에서 공통적으로 활용하기 좋은 스프링에서 제공해 주는 기능
    + web MVC
        - HTTP 요청 응답 처리 : web MVC는 계속 어플리케이션이 요청 올때까지 대기를 하고 있음, 요청이 오면 그 즉시 처리를 하고 응답을 함
        - 필터, 인터셉터 : 웹에서 들어오는 요청을 필터링 하거나 공통적인 처리를 하는 용도의 기술
        - 예외처리 : 웹에서 들어오느 처리를 하던 중 오류가 발생 했을 때 어떻게 해야 안전하게 처리할 수 있을까?
    + Bean이란 Spring에서 자동으로 관리하는 객체, 기존 자바에서는 개발자가 직접 생성자를 생성하고 그것에 맞는 또다른 생성자를 넣고 하는 작업이 필요했는데 이걸 Spring 프레임 워크에서 알아서 해주기위한 객체이다.
    + DI,IoC,컨테이너
        - DI (Dependency injection)- 의존성 주입
            - 의존성이란 서로 다른 클래스나 함수가 서로 연관을 가지고 있는 것
        - IoC (Inversion Of control) - 제어의 역전
            - 사용자가 직접 클래스를 생성 하지 않고, 프레임워크(스프링)이 제어하도록 함
            - 만약 사용자가 무언가를 하게 하고 싶으면
                - Bean LifeCycle callback 사용
                    - CallBack 함수란?
                        - 의미 자체에선 나중에 호출 되는 함수
                        - 여기서 나중이란 정의된 시점을 의미
                - Bean LifeCycle
    + DI 설정의 역사
        - 1.XML을 통한 빈 등록
            - 토비의 스프링, 스프링3/4 시절까지 많이 사용
            - 설정이 외부로 명확히 분리된 것을 알 수 있음
            - 하지만 자동완성이나 컴파일 등으로 오타를 잡기 어렵고 타이핑 양이 많아짐
            - 방법
                - 만들어진 spring 프로젝트에서 기본적으로 구동될 프로그램을 작성
                - resources에 XML Config File 생성
                - 의존성 주입(존나 어렵게 말하는데 그냥 함수의 구성이 어떻게 생격먹었나를 넣어주는것)
        - 2.XML ComponentScan을 통한 빈 등록
            - 기본 빈 등록 방식은 클래스가 많을 경우 너무 번거로움
            - @Controller, @RestController, @Service, @Component, @Repository 등의 지정된 어노테이션으로 사용에 맞게 각 클래스마다 빈으로 등록해줌
        - 3.JavaConfig를 통한 빈 등록
            - 스프링4 때부터 XML이 아닌 JavaConfig가 많이 활용되기 시작
            - XML 설정파일이 자바 코드화 된 것
            - 자동완성, 컴파일 시 정적 분석으로 오류를 잡아줌
            - 즉, 자바코드 단에서 잡아주겠다.
        - 4.JavaConfig ComponeneScan을 통한 등록
            - XML의 설정방법과 JavaConfig에서의 설정 방식의 혼합 형태
            - 스프링 부트에서는 이 방식이 기본적으로 적용
    + 다양한 빈 관련 설정 방법
        - 빈의 구현체가 여러개인(즉, 입력받을 객체 타입이 상속을 받아서 여러개가 되었고 동일 레벨일때, 어떤녀석을 우선적으로 넣을 것이냐) 경우 주입 방법
            1. @Primary : 해당 빈을 최우선으로 주입
            2. @Qualifier("beanName") : beanName을 클래스 이름으로 가진 빈을 주입
            3. Set또는 List로 모두 받기 : Set이나 List로 받아서 어떤 타입으로 결정된 객체가 들어오면 그것으로 수행하게
            4. 프로퍼티 이름을 빈과 동일하게 하기(그냥 매개변수 이름을 빈의 이름과 같게쓴다는 말을 젖같이 어렵게 쓴거임): 가장 흔하게 사용하는 방법이다.
        - 빈의 스코프
            1. 싱글톤 : 일반적인 방법, 하나만 만들어서 계속 재사용한다, 멀티 쓰레드 환경에 부적합하다.
            2. 프로토타입 : 매번 새로 만드는 방법(데이터를 클랜징 해야할 때)
                - request : 요청에 따라 계속 새로 만듦
                - session : 세션마다 계속 새로 만듦
        - 스프링 환경 설정 : 프로파일
            - 현업에서는 환경을 다양하게 하여 해당 환경에서만 동작하는 빈을 맏느는 경우가 있다.
            - 클래스 단위에 적용하거나 메서드 단위에 적용 가능
                - 클래스 단위
                    - @Configuration @Profile("test")
                    - @Component @Profile("test")
                - 메서드 단위
                    - @Bean @Profile("test")
            - -spring.profile.active = sandbox,beta,production과 같이 설정을 해줘야함 edit configuration(인텔리제이 실행 왼쪽)에서 환경변수 입력
            - 프로파일 표현식
                - @Profile("!production")
                - !(not),&(and),|(or)
    
4. 스프링의 부가기능
    + 외부자원 가져오기(Resource)
        - 리소스 종류
            - URL - java.net.URL을 래핑한 버전, 다양한 종류(ftp;,file;,http;, 등의 prefix로 접근 유형 판단)의 Resource에 접근 가능 하지만 기본적으로는 http(s)로 원격 접근
            - classpath(기본적으로 target/classes폴더에 있는 소스코드를 빌드한 결과) 하위의 리소스 접근 시 사용
            - FileSystemResource - 파일 관련 리소스
            - ServletContextResource - Servelet 어플리케이션 루트 하위 파일
            - InputStreamResource - InputStream
            - ByteArrayResource - ByteStream
        - 리소스를 로딩할 때 사용하는 기능
            - 기본적으로 applicationContext에서 구현이 되어 있음
            - 프로젝트 내 파일(주로 calsspath)에 접근할 일이 있을 경우 활용
            - 대부분 사전 정의 된 파일들은 자동으로 로딩되도록 되어 있으나, 추가로 필요한 파일이 있을 때 이부분 활용 가능
    + AOP(관점 지향 프로그래밍)
        - 공통적인 관심사(로긴, 트랜젝션, 인증)를 여러 메서드 호출 전/후에 원할때마다 손쉽게 추가
        - OOP로 처리하기에는 다소 까다로운 부분을 AOP라는 처리 방식을 도입하여 손쉽게 공통 기능을 추가/수정/삭제 할 수 있도록 함
        - 기본 개념
            - Aspect(관점, 관심)
                - 여러 클래스나 기능에 걸쳐서 있는 관심사, 그리고 그것들을 모듈화
                - AOP 중에서 가장 많이 활용되는 부분은 @Transactional, @Cacheable 기능
            - Advice(조언)
                - AOP에서 실제로 적용하는 기능(로긴, 트렌젝션, 캐시, 인증 등)을 뜻함
            - Join Point(연결 포인트)
                - 모듈화된 특정 기능이 실행될 수 있는 연결 포인트
            - Pointcut(포인트 선택 방법)
                - Join point 중에서 해당 Aspect를 적용할 대상을 뽑을 조건식
            - Target Object
                - Advice가 적용될 대상 오브젝트
            - AOP Proxy
                - 대상 오브젝트에 Aspect를 적용하는 경우 Advice를 위해 하는 작업을 AOP Porxy라고 함
                - 주로 CGLIB(Code Generation Library, 실행 중에 실시간으로 코드를 생성하는 라이브러리) 프록시를 사용하여 프록싱 처리
            - Weaving
                - Advice를 비즈니스 로직 코드에 삽입하는 것을 말함
        - AspecctJ 지원
            - AspectJ는 AOP를 제대로 사용하기 위해 꼭 필요한 라이브러리
            - 기본적으로 제공되는 Spring AOP로는 다양한 기법(Pointcut 등)의 AOP를 사용할 수 없음
            - Aspect 생성
            ```Java
                package org.xyz;
                import org.aspectj.lang.annotation.Aspect;

                @Aspect
                @Component // Aspect를 스프링의 Bean으로 등록하기 위해 사용
                public class UsefulAspect {

                }
            ```
            - Pointcut 선언
            ```Java
                package org.xyz;
                import org.aspectj.lang.annotation.Aspect;

                @Aspect
                @Component // Aspect를 스프링의 Bean으로 등록하기 위해 사용
                public class UsefulAspect {
                    @Pointcut("excution(* transfer(..))")
                    private void anyOldTransfer() {}
                }
            ```
            - 해당 Aspect의 Advice(실행할 액션이) 적용될 Join point를 찾기위한 패턴 또는 조건 생성
            - 포인트 컷 포현식이라고 부름
            - Pointcut 결합
            ```Java
                package org.xyz;
                import org.aspectj.lang.annotation.Aspect;

                @Aspect
                @Component // Aspect를 스프링의 Bean으로 등록하기 위해 사용
                public class UsefulAspect {
                    @Pointcut("excution(* transfer(..))")
                    private void anyOldTransfer() {} // public 메서드 대상 포인트 컷

                    @Pointcut("within(com.xyz.myapp.trading..*)")
                    private void inTrading() {} // 특정 패키지 대상 포인트 컷
                    
                    @Pointcut("anyPublicOperation() && inTrading()")
                    private void traindgOperation() {} // 위의 두 조건을 and(&&) 조건으로 결합한 포인트 컷
                }
            ```
            - Advice 정의
                - 포인트 컷들을 활용하여 포인트컷의 전/후/주변에서 실행될 액션을 정의함
                - Before Advice
                    - dataAccessOperation()이라는 미리 정의된 포인트 컷 바로 전에 doAccessCheck가 실행
                ```Java
                    import org.aspectj.lang.annotation.Aspect;
                    import org.aspectj.lang.annotation.Before;

                    @Aspect
                    public class BeforeExample {
                        @Before("com.xyz.myapp.CommonPointCuts.dataAccessOperation()")
                        public void doAccessCheck() {
                            //...
                        }
                    }
                ```
                - After Returning Advice
                    - dataAccessOperation()라는 미리 정의된 포인트 컷에서 return이 발생된 후 실행
                ```Java
                    import org.aspectj.lang.annotation.Aspect;
                    import org.aspectj.lang.annotation.AfterReturning;

                    @Aspect
                    public class BeforeExample {
                        @AfterReturning("com.xyz.myapp.CommonPointCuts.dataAccessOperation()")
                        public void doAccessCheck() {
                            //...
                        }
                    }
                ```
                - Around Advice
                    - businessService()라는 포인트 컷의 전/후에 필요한 동작을 추가
                ```Java
                    import org.aspectj.lang.annotation.Aspect;
                    import org.aspectj.lang.annotation.Around;
                    import org.aspectj.lang.ProceedingJoinPoint;

                    @Aspect
                    public class BeforeExample {
                        @Around("com.xyz.myapp.CommonPointCuts.businessService()")
                        public void Object doBasicProfiling(ProceedingJoinPoint pjp) throws Throwable {
                            // start stopwatch
                            Object retVal = pjp.proceed();
                            // stop stopwatch
                            return retVal;
                        }
                    }
                ```
    + Validation, Data Binding
        - Validation(유효성 검증)
            - 주로 사용자 또는 타 서버의 요청 내용에서 잘못된 내용이 있는지 확인하는 행위를 뜻함
            - Validation의 종류
                - 데이터 검증
                    - 필수 데이터의 존재 유무
                    - 문자열의 길이나 숫자형 데이터의 경우 값의 범위
                    - email, 신용카드 번호등 특정 형식에 맞춘 데이터
                - 비즈니스 검증
                    - 서비스에 정책에 따라 데이터를 확인하여 검증
                    - 에) 배달앱인 경우 배달 요청을 할 때 해당 주문건이 결제 완료 상태인지 확인등
                    - 경우에 따라 외부 API를 호출하거나 DB의 데이터까지 조회하여 검증하는 경우도 존재
            - Spring의 Validation
                - 스프링은 웹 레이어에 종속적이지 않은 방법으로 벨리데이션을 하려고 의도하고 있으며 주로 아래 두가지 방법을 활용하여 벨리데이션 진행(둘다 데이터 검증에 가까움)
            - Java Bean Validation
                - JavaBean 기반으로 간편하게 개별 데이터를 검증
                - 요즘 가장 많이 활용되는 방법 중 하나이며, JavaBean 내에 어노테이션으로 검증 방법을 명시
                - 이후 @Valid 어노테이션을 해당 @RequestBody에 달게 되면 Java Bean Validation을 수행 하 문제가 없을 때만 메서드 내부로 진입
                - 검증 실패시 MethodArgumentNotValidException이 발생
            - Validation 수행 시 주의 사항 및 패턴
                - 주의 사항
                    - validation이 너무 여러 군데에 흩어져 있으면 테스트 및 유지 보수성이 떨어짐
                        - 중복된 검증 : 정책 변경 시에 모든 중복 코드를 수정해야 함
                        - 다른 검증 : 여러 군데서 다른 정책을 따르는 검증이 수행될 수 있음
                    - 가능한 validation은 로직 초기에 수행 후 실패 시에는 exception을 던지는 편이 처리가 편이
        - Data Binding
            - 사용자나 외부 서버의 요청 데이터를 특정 도메인 객체에 저장해서 우리 프로그램에 Request에 담아주는 것을 뜻함
                - Converter<S,T> Interface
                    - S라는 타입을 받아서 T라는 타입으로 변화해주는 Interface
                ```Java
                    package org.springframework.core.convert.converter;

                    public interface ConverterM<S,T> {
                        T convert(S source);
                    }
                ```
                - Formatter
                    - 특정 객체 <-> String 간의 변환을 담당
    + 스프링 표현 언어(SpEL)
        - 짧고 간단한 문법을 통해 필요한 데이터나 설정 값을 얻어올 수 있게 하는 특별한 형태의 표현식에 가까운 간편한 언어


5. 스프링 MVC(Model,View,Controller)
    + 국내 IT 서비스 실무에서 가장 많이 활용되는 웹 개발 기술
    + 웹 개발 기술은 시대에 따라 계속 변화
        - HTML -> CGI -> Servlet -> Spring MVC -> ???(node,ktor,etc)
    + MVC 패턴
        - 이전 MVC 에서는 비즈니스 화면과 데이터가 분리되지 않아 너무 많은 역할을 하게됨 MVC 패턴을 쓰고 나서 부터는 역할이 분담되고, 자기 할일만 하도록 분리
    + Spring MVC의 아키 텍쳐
        - 강의자료 참고
        - 결국 핵심은 M,V,C
            - Model : 로직 안에서 이동하고 있는 데이터
            - View : 화면은 화면의 역할만 한다
            - Controller : 비즈니스 로직을 처리하고 모델과 뷰를 응답으로 준다.
    + 스프링 MVC 기본 HTTP 요청 매핑
        - Controller/RestController의 차이
            - Controller : 응답 값이 기본적으로 HTML을 주도록 되어 있음
            - RestController : 응답 값으로 Rest API 요청에 대한 응답 (주로 JSON)을 주도록 되어 있음
        - 매핑 어노테이션
            - @RequestMapping : GET,POST 등 요청 방식을 직접 지정
            ```Java
                @RequestMapping(value = "/order/1", method = RequestMethod.GET)
                public String getOrder() {
                    log.info("Get some order information");
                    return "orderId:1, orderAmount:100";
                }
            ```
        - 축약형 매핑 어노테이션
            - @GetMapping : 데이터를 가져옴
            - @PostMapping : 데이터를 전송함
            - @PutMapping : 전체 수정
            - @PatchMapping : 일부 수정
            - @DeleteMapping : 삭제
            ```Java
                @PostMapping("/order/1")
                public String createOrder() {
                    log.info("Create order");
                    return "orderId:2, orderAmount:1000";
                }
            ```
        - 스프링 HTTP요청 파라미터 전송
            - 파라미터를 넘기는 방법
                - Get, Delete
                    - PathVariable: 요새는 id를 path에 넣는 것을 선호
                        - 하지만 이름이 같으면 생략 가능
                        - 여러개를 넣을 수 있음
                    - query-params: 추기적인 정보들 입력
                        - 게시판의 검색 필터 페이징에서 많이 사용
                        - @RequestParam 사용법
                            - PathVariable처럼 이름을 동일하게 하면 자동으로 받아줌
                            - required, defaultValue 옵션 설명
                            - 사실 없어도 자동으로 나옴
                            - Map, MultiValueMap으로 요청 받는 방법
                        ```Java
                            //파라미터를 받아온다
                            //URL Path 에 직접 가져오는
                            @GetMapping (value = "/order/{orderId}")
                            public String getOrder2(@PathVariable String orderId) {
                            log.info("Get some order" + orderId);
                            return "orderId:"+ orderId +", orderAmount:1000";
                            }

                            //추가 정보 입력할 때
                            @GetMapping (value = "/order")
                            public String getOrder2(@RequestParam String orderId) {
                            log.info("Get some order" + orderId);
                            return "orderId:"+ orderId +", orderAmount:1000";
                            }
                        ```
                - Post, Put, Patch
                    - @RequestBody : http body 정보를 편리하게 받을 수 있음
                        - 주로 사용하는 메시지 포맷은 JSON(현재 사실상의 표준)
                    - @RequestHeader : http header 정보를 편리하게 받을 수 있음
    + 스프링 MVC - 필터, 인터셉터
        - 필터
            - 스프링 외의 서블릿에서 제공하는 공통처리 기능
            - 스프링 내로 요청이 들어오기 전과 스프링의 요청이 나갈 때 처리 가능
            - 조금 더 low level 처리가 가능
            - 좋은 레스토랑에 처음 들어갈 때 옷에 묻은 먼지를 털듯 제일 앞에서 필터링 해주는 곳
        ```Java
        //필터 예시            
            @Slf4j
            @Component
            //필터 사용 예시
            public class LogFilter implements Filter {
                @Override
                public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
                        throws IOException, ServletException {
                    // 외부 -> Filter (-> 처리 ->) Filter -> 외부
                    // 아래가 ()내요임 계속해서 chainnig 하며 여러 필터를 거칠 수 있게함
                    log.info("Hello filter : " + Thread.currentThread());
                    chain.doFilter(request,response);
                    log.info("Bye LogFilter : " + Thread.currentThread());
                }
            }

            //보통 Component로 직접 등록(위에서 @Component)하는 것보다
            //아래와같이 설정 파일을 만들어 빈으로 등록하여 사용 (위에 @Component 제거)
            @Configuration
            public class WebConfig implements WebMvcConfigurer/*WebMVC를 설정하는 Configurer를 생성 하겠다*/ {
                @Bean//필터를 등록해주기 위한 Bean
                public FilterRegistrationBean loggingFilter() {
                    FilterRegistrationBean<Filter> filterFilterRegistrationBean = new           FilterRegistrationBean<>();
                    filterFilterRegistrationBean.setFilter(new LogFilter());
                    filterFilterRegistrationBean.setOrder(1);// 여러 필터가 존재할때 우선순위           정해주기
                    filterFilterRegistrationBean.addUrlPatterns("/*");

                    return filterFilterRegistrationBean;
                }
            }
        ```
        - 인터셉터
            - 스프링에서 제공하는 공통처리 기능
            - 실제 매핑된 Handler 정보를 확인 가능(어떤 것이 실제 내 요청을 처리하는지도 확인 가능)
            - 조금 더 상세한 조건식과, 세부적인 스펙(pre,post,after)를 통해 구체적인 동작 가능
            - AOP와 비교한다면 AOP는 인터셉터보다 구체적인 조건(어노테이션, 파라미터, 주소 등)과 동작 위치를 가짐
        ```Java
            @Slf4j
            //바로 못쓰기 때문에 등록을 해줘야한다.
            public class LogInterceptor implements HandlerInterceptor {
            
                @Override
                public boolean preHandle(HttpServletRequest request, 
                                         HttpServletResponse response, 
                                         Object handler) throws Exception {
                    log.info("preHandle LogInterceptor : " + Thread.currentThread());
                    log.info("preHandle handler : " + handler);

                    return true;//다음 요청이 이어지길 원하면 true 아니면 false
                }

                @Override
                public void postHandle(HttpServletRequest request, 
                                       HttpServletResponse response, 
                                       Object handler, 
                                       ModelAndView modelAndView) throws Exception {
                    //View 단위까지 확인 가능
                    log.info("postHandle LogInterceptor : " + Thread.currentThread());

                }

                //exception이 발생하면 postHandle 안탐
                //그리고 에러페이지 띄어주면서 그 해당페이지에 대한 interceptor가 재발생
                @Override
                public void afterCompletion(HttpServletRequest request, 
                                            HttpServletResponse response, 
                                            Object handler, 
                                            Exception ex) throws Exception {
                    log.info("afterCompletion LogInterceptor : " + Thread.currentThread());

                    if(ex != null) {
                        log.error("afterCompletion exception : " + ex.getMessage());
                    }
                }
            }
            @Configuration
            public class WebConfig implements WebMvcConfigurer/*WebMVC를 설정하는 Configurer를 생성 하겠다*/ {
                @Bean//필터를 등록해주기 위한 Bean
                public FilterRegistrationBean loggingFilter() {
                    FilterRegistrationBean<Filter> filterFilterRegistrationBean
                        = new FilterRegistrationBean<>();
                    filterFilterRegistrationBean.setFilter(new LogFilter());
                    filterFilterRegistrationBean.setOrder(1);// 여러 필터가 존재할때 우선순위 정해주기
                    filterFilterRegistrationBean.addUrlPatterns("/*");

                    return filterFilterRegistrationBean;
                }

                //인터 셉터 설정
                @Override
                public void addInterceptors(InterceptorRegistry registry) {
                    registry.addInterceptor(new LogInterceptor())
                    .order(1).addPathPatterns("/**")
                    .excludePathPatterns("/css/*","/images/*");
                }
            }
        ```
    + 스프링 MVC - 예외 처리
        - @ExceptionHandler()
        - 컨트롤러 기반 예외 처리
        ```Java
            @Slf4j//Lombok내용 나중에
            @RestController// Rest API를 사용하는 컨트롤러를 사용하겠다.
            public class SampleController {                       
                //상테에러 코드 변경
                @ResponseStatus(HttpStatus.FORBIDDEN)
                //예외 헨들러
                @ExceptionHandler(IllegalAccessException.class)
                public ErrorResponse handleIllegalAccessException(
                        IllegalAccessException e){
                    log.error("IllegalAccessException is occurred.",e);

                    return new ErrorResponse("INVALID_ACCESS",
                            "IllegalAccessException");
                }
            }
        ```
        - HTTp Status code를 변경하는 방법
            - @ResponseStatus
            - ResponseEntity 활용
        ```Java
            @Slf4j//Lombok내용 나중에
            @RestController// Rest API를 사용하는 컨트롤러를 사용하겠다.
            public class SampleController {
                //예외 헨들러
                @ExceptionHandler(IllegalAccessException.class)
                public ResponseEntity<ErrorResponse> handleIllegalAccessException(
                        IllegalAccessException e){
                    log.error("IllegalAccessException is occurred.",e);
                    
                    return ResponseEntity
                            .status(HttpStatus.FORBIDDEN)//상태코드 변경
                            .header("newHeader","Some Value")//헤더 붙여주기
                            .body(new ErrorResponse("INVALID_ACCESS",
                                    "IllegalAccessException"));//전달할 예외 내용
                }
            }
        ```
        - 예외처리 우선 순위
            1. 해당 exception이 정확히 지정된 Handler
            2. 해당 exception의 부모 예외 Handler
            3. 이도 저도 아니먄 그냥 Exception(모든 예외의 부모)

6. Lombok
    - 자바의 보일러 프레이트 코드를 줄여주는 라이브러리
    - Lombok의 기능들
        - @Setter, @Getter : Java Bean 규약에 있는 setter, getter를 자동 생성
        - @ToString : Object에 기본 구현된 ToString 대신 객체의 데이터를 보여주는 ToString을 자동 생성
        - @NoArgsConstructor, @AllArgsConstruct

7. H2 DB 설명과 활용
    + 간단히 활용해 볼 수 있는 메모리/파일 관계형 DB이며 가볍고 빠르기 때문에 개발 단계에 활용하거나 테스트 코드에도 자주 활용되는 DB
    + 활용은 프로젝트 예제에서 직접 참고

8. 트랜젝션 개념 - DB 이론 공부하면서 다시 해보자
    + ACID
        - Atomic(원자성)
            - All or Nothing, 모든 작업이 실행되거나 혹은 모두 실행되지 않아야 한다.
            - 예시) A 계좌에서 B 계좌로 작액을 송금할 때
                - 'A 계좌 잔액 줄이기' 작업과 'B계좌 잔액 늘리기' 작업은 함께 성공하거나 함께 실패해야 한다.
        - Consistency(일관성)
            - 모든 트랜젝션이 종료된 후에는 DB의 제약조건을 모두 지키고 있는 상태가 되어야 한다.
            - 예시) 작액은 0원 이상이다.
                - 이를 위반하는 트랜잭션은 모두 중단된다.
        - Isolation(격리성)
            - 트랜젝션은 다른 트랜잭션과 독립적으로 동작해야 한다.
            - A 트랜젝션이 하는 일을 B 트랜젝션은 모르게 해야한다.
        - Durability(지속성)
            - commit을 하게 되면 지속(저장)이 꼭 된다.
            - DB 저장이 실패하더라도 모든 로그를 모두 남겨서 DB에 순차적으로 모두 반영이 되도록 한다.

9. Embeded Redis
    - Key, Value 구조의 비정형 데이터를 저장하고 관리하기 위한 오픈 소스 기반의 비관계형 데이터 베이스 관리 시스템 (DBMS)입니다. 
    - 데이터베이스, 캐시, 메세지 브로커로 사용되며 인메모리 데이터 구조를 가진 저장소입니다.
    - 사용 목적
        - SpinLock을 활용한 동시성 제어
        - 동시성 제어를 AOP를 활용하여 실습하는데 활용되는 인프라
    - LocalRedis 실행 설정
        - Spring Boot가 기동하면서, Bean을 등록할 때 레디스를 실행하고, 종료되면서 Bean을 삭제할 때 레디스를 종료하도록 설정
        - 주의점 : 해당 Bean이 Redis Repository보다 빨리 뜰 수 있도록 패키지 순서를 위쪽으로 해야 한다.

10. 테스트 (Junit, Mockito, Spring boot)
    + xUnit이라는 유닛테스트 프레임 워크의 일환으로 Java용으로 개발된 프레임워크
        - Junit은 단위 테스트를 실행하고 결과를 검증해서 전체 결과를 리포트해주는 프레임 워크
        - 사용자가 직접 동작시킬 수도 있으며 Gradle이나 Maven 등으로 통해 빌드하면서 테스트 가능
        - spring-boot-starter-test에 기본적으로 Junit5 포함됨
        
        
        
            