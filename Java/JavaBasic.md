# 자바 기초
1. 자바 프로그램 실행 과정
    + 소스코드 작성
    + 자바 컴파일러에서 컴파일
    + 바이트코드로 변환(program.class)
    + 각 운영체제의 맞는 JVM이 실행
***
2. 자바 플렛폼
    + JavaSE -> 기본 개발 환경
    + JavaEE -> 서버기반 프로그램 개발 환경
    + JavaME -> 모바일, 임베디드 시스템 개발 환경 
***
3.  변수와 자료형
    + 변수 - 데이터를 저장하는 메모리 공간에 붙여준 이름
        ```Java
            //데이터 타입 변수명 = 값;
            int age = 20;
            String country = "Korea";
        ```
    + 대부분의 자료형은 C++와 비슷함 아래는 처음보는 것 정리, 각 자료형에 대한 메소드는 구글링 참조 할 것
        - StringBuffer - 문자열을 자주 추가하거나 변경할때 사용하는 자료형
        ```Java
            StringBuffer sb1 = new StringBuffer("HelloWorld");
            sb1.append("!!!");  //sb1 -> "HelloWorld!!!"
        ```
        - List - 배열과 같이 여러 데이터를 담을 수 있는 자료형, 메모리상 연속적으로 존재하지 않는다.
        ```Java
            ArrayList l1 = new ArrayList();
        ```
        - Map - Key,Value 형태로 데이터를 저장하는 자료형
        ```Java
            HashMap<String,String> map = new HashMap<String,String>();
        ```
        - Generics - 자료형을 명시적으로 지정, 제한적일 수 있지만 안정성을 높여주고 형변환을 줄여줌
            - 또한 어떤 제한을 건다고 생각하면 좋음
        ```Java
            ArrayList<String> l1 = new ArrayList<String>(); //<>안에 있는 것들을 Generics라고 함
        ```
    + 문자열 메소드
        - equals - 문자열 비교 메소드
        ```Java
            String s1 = "HI"
            String s2 = "Bye";
            s1.equals(s2)  //false
        ```
        - indexOf - 특정 문자를 찾아 반환하는 매소드
        ```Java
            String s3 = "Hello! World";
            s3.indexOf("!",s3) // 5
        ```
        - replace - 특정 문자열을 찾아 바뀐 문자열을 반환하는 메소드(원래 문자열은 안변함)
        ```Java
            String s4 = s3.replace("Hello","Bye");//s4 -> "Bye World"
                                                  //s3 -> "Hello World"
        ```
        - substring - 문자열을 잘라 반환 해주는 메소드
        ```Java
            String s5 = s3.substring(0,3) //s5 -> "Bye"
                                          //s3 -> "Bye World"
        ```
        - toUpperCase - 소문자를 대문자로 바꿔주는 메소드
        ```Java
            String s6 = s4.toUpperCase() //s5 -> "BYE"
                                         //s4 -> "Bye"
        ```
***
4. 연산자
    + 기본 연산자는 다른 언어와 모두 같다
    + 2진법 - 컴퓨터에서 데이터 표현에 사용,2를 기반으로 하는 숫자체계
        - 2의 보수 - 2의 제곱수에서 빼서 얻은 이진수
            - 2진수 3의 2의 보수 : 11의 보수 = 01
            - 2의 보수를 빠르게 계산 하기 위해선 비트 전부 반전 + 1
    + \>\>\> 연산자 - 비트를 오른쪽으로 이동한다.
        - \>\> 연산자와 다른 점은 앞의 비트를 모두 0으로 채워버린다.
***
5. 조건문,반복문
    + 기본 반복문 조건문은 C++과 같음
    + for each문
    ```Java
        int[] nums = {1,2,3,4,5};
        for(int val : nums) {
            System.out.println(val);
        }
    ```
***
6. 클래스와 객체
    + 클래스 - 객체를 정의하는 설계도
        - 클래스의 객체가 인스턴스화될 때 자동으로 호출되는 특수한 종류의 멤버 함수다.
        - 생성자 규칙
            - 클래스명과 이름을 맞춘다
            - 리턴 타입이 없다.
    ```Java
        public class className{
            //멤버 변수
            //메소드
            //+ 접근 제어자
            //+ static
        }
    ```
    + 객체 - 클래스를 기반으로 만들어진 실체
    ```Java
        Car car = new Car() // 객체의 생성
    ```
    + 인스턴스 - 클래스와 객체의 관계
        - 클래스로부터 객체를 선언 (인스턴스 화)
        - 어떤 객체는 어떤 클래스의 인스턴스
    + 오버로딩 - 한 클래스 내에서 같은 이름의 메소드를 여러개 정의 한것
        - 조건
            1. 메소드의 이름이 같아야함
            2. 매개변수의 개수 또는 타입이 달라야함
    + 접근제어자 - 클래스의 변수나 메소드의 접근에 제한을 두는 키워드
        - private : 해당 클래스에서만 접근 가능
        - public : 어디서든 접근 가능
        - default : 해당 패키지 내에서만 접근 가능
        - protected : 해당 패키지 및 상속받은 클래스에서 접근 가능
    + Static - 변수나 메소드의 특성을 바꿈
        - 메모리에 한번만 할당됨
        - 즉, Static 변수나 메소드는 공유되는 특성을 가짐
***
7. 상속
    + 상속 - 기존 클래스에 기능 추가 및 재정의하여 새로운 클래스를 정의
        - 부모 클래스 : 상속 대상이 되는 기존 클래스
        - 자식 클래스 : 기존 클래스를 상속하는 클래스
        - 부모 클래스의 필드와 메소드가 상속됨 (생성자 초기화 블록은 상속 안됨)
        - 다중 상속은 불가능
        ```Java
            class childCalss extends parentCalss{
                //필드;
                //메소드;
                //...
            }
        ```
        - super,super()
            - super - 부모 클래스와 자식클래스의 멤버 이름이 같을 때 구분하는 키워드
            - super() - 부모 클래스의 생성자 호출
    + 오버라이딩 - 부모 클래스의 메소드를 자식 클래스에서 재정의
        - 조건
            1. 메소드의 선언부는 부모 클래스의 메소드와 동일해야함
            2. 반환 타입에 한해서, 부모 클래스의 반환 타입으로 변환할 수 있는 타입 변경가능
            3. 부모클래스의 메소드보다 접근제어자를 더 좁은 범위로 변경 불가
            4. 부모 클래스의 메소드 보다 더 큰 범위의 예외 선언 불가
***
8. 다형성
    + 다형성 - 한 객체가 여러 가지 타입을 가질 수 있는 것
    + 부모 클래스 타입의 참조 변수로 자식 클래스 인스턴스 참조
    ```Java
        class Person{}
        class Student extends Person{}

        Person p1 = new Student(); // Student가 Person을 상속 받고 있기 때문에 가능 하다. =>  업캐스팅
        //Student s1 = new Person() 자식이 부모의 객체를 받을 수 없기 때문에
        //불가능하다
        System.out.println(p1 instanceof Person);
        // instance of 함수로 p1이 Person의 자식인지 확인 할 수 있다.
    ```
    + 업 캐스팅 - 부모 객체가 자식의 클래스로 인스턴스화 하는것
    + 다운 캐스팅 - 자식 객체에 부모 객체를 형변환 하여 가리키는 것
***
9. 추상 클래스
    + 추상 메소드 - 자식 클래스에서 반드시 오버라이딩 해야하는 메소드, 선언만 하고 구현 내용 없음
    ```Java
        //즉, 오버라이딩 해야만 한다고 지정한다. 생각하면 된다.
        abstract void print();
    ```
    + 추상 클래스 - 추상 메소드를 하나이상 포함하는 클래스, 반드시 구현해야하는 부분에 대해 명시적으로 표현, 추상 클래스 자체는 객체 생성 불가
    ```Java
        //추상 클래스
        abstract class className{
            abstract void print();
        }
    ```
***
10. 인터페이스
    + 인터페이스 - 다중 상속처럼 사용할 수 있는 기능, 추상 메소드와 상수만으로 이루어짐
    ```Java
        //접근제어자 interface 인터페이스 이름{}의 형식으로 선언
        //인터페이스의 선언
        public interface interfaceName(){
            public static final int Name = "GaJaMy";
            public abstract void print();
        }

        class className implements interfaceName(){

        }
    ```
    + 동시 사용으로 다중 상속과 같은 효과를 낼 수 있음
    ```Java
        public interface interface1{

        }

        public class class1{

        }

        class class2 extends class1 implements interface1{

        }
    ```
***
11. 내부 클래스
    + 내부 클래스 - 클래스 안에 선언한 클래스
        - 내부 클래스에서 외브 클래스 멤법 접근 가능
        - 외부에서는 내부 클래스에 접근 불가
        - 종류
            1. 인스턴스 클래스 -
            2. 정적 클래스 - 메모리에 바로 상주 되는 클래스
            3. 지역 클래스 - 클래스의 메소드 안에 선언 되는 클래스
            4. 익명 클래스 - 이름을 가지지 않는 클래스, 선언과 동시에 객체 생성, 일회용 클래스
    ```Java
        // 익명 클래스 사용을 위한 추상 클래스
        abstract class Person{
            public abstract void printInfo();
        }
        
        class Student extends Perse{
            public void printInfo(){
                System.out.println("Student.printInfo");
            }
        }

        // 내부 클래스 구조
        class Outer{
            public void print(){
                System.out.println("Outer.print")
            }

            // 인스턴스 클래스
            class Inner{
                public void innerPrint(){
                    Outer.this.print() //외부 클래스의 메소드 접근 가능
                }
            }

            //정적 클래스
            static class InnerStaticClass{
                void innerPrint(){
                    //아래가 에러 발생 -> 외부 클래스 메소드 접근 불가능
                    //Outer.this.print();
                }
            }

            //익명 클래스
            Person p1 = new Person(){
                @Override
                public void printInfo(){
                    System.out.println("Main.printInfo");
                }
            };
        }
    ```
***
12. 입출력
    + 콘솔 입력 - 입출력 방식 중 콘솔 입력 방법 ,  각 방식들 검색해서 사용법 익힐 것(강의 13-1 Java 입출력_1)
    ```Java
        System.in.read();
        InputStreamReader reader;
        BufferedReader br;
        Scanner sc; //이 방식을 자주 사용
    ```
    + 콘솔 출력 - 입출력 방식 중 콘솔 출력 방법 , 입력과 마찬가지
    ```Java
        System.out.println();//외 여러가지
    ```
    + 파일 출력
    ```Java
        //FIleWriter를 사용하는 방법
        FileWriter fw = new FileWriter("test.txt");
        String memo = "test\n";
        fw.write(memo);
        fw.close();

        //PrintWriter를 사용하는 방법
        PrintWriter pw = new PrintWriter("test.txt");
        memo = "test2";
        pw.println(memo);
        pw.close();
    ```
    + 파일 입력
    ```Java
        //BufferedReader 사용법
        BufferedReader br = new Buffered(new FileReader("test.txt"));
        br.close();
    ```
***
13. 예외 처리
    + 예외(Exception) - 정상적이지 않은 Case
    ```Java
        //예외 핸들링 하는 방법 try catch
        try{
            ...
        } catch(예외 case1){
            ...
        } catch(예외 case2){
            ...
        }
    ```
    + finally - 예외 발생 여부와 관계없이 항상 실행되는 부분
    ```Java
        try{
            //예외가 발생할 수도 있는 부분;
        }catch (예외 case1){
            //예외 case1이 발생해야 실행되는 부분;
        }finally{
            //항상 실행되는 부분;
        }
    ```
    + throw,throws
        - throw - 예외를 발생 시킴
        - throws - 예외를 전가 시킴
    ```Java
        void Func1(){
            throw new Exception();
        }

        void Func2() throws Exception{
            ...
        }
    ```
    + 사용자 정의 Exception
    ```Java
        //RuntimeExcetion을 상속받아 새롭게 정의해서
        //발생시킨다.
        class ExcetionName extends RuntimeExcetion{

        }
    ```
***
14. 컬렉션 프레임 워크
    + 여러 데이터를 편리하게 관리할 수 있게 만들어 놓은것
        - 자료 구조 및 알고리즘을 구조화
    + 대표 인터페이스
        - List 인터페이스, Set 인터페이스, Map 인터페이스
    + List 인터페이스
        - 순서가 있는 데이터의 집합
        - 데이터 중복 허용
    ```Java
        ArrayList list1 = new ArrayList();
        LinkedList list2 = new LinkedList();
        Vector v = new Vector();
    ```
    + Set 인터페이스
        - 순서가 없는 데이터의 집합
        - 데이터의 중복 허용 하지 않음
    ```Java
        HashSet set1 = new HashSet();
        TreeSet set2 = new TreeSet();
    ```
    + Map 인터페이스
        - 키와 값의 쌍으로 이루어진 데이터 집합
        - 순서를 유지 하지 않음
    ```Java
        HashMap map1 = new HashMap();
        TreeMap map2 = new TreeMap();
    ```
***
15. 스트림
    + 배열, 컬렉션 등의 데이터를 하나씩 참조하여 처리 가능한 기능
    + for문의 사용을 줄여 코드를 간결하게 함
    + 개인적으론 사용 안할듯 람다식과 마찬가지로 가독성 떨어짐
    + 사용하고 싶으면 검색해서 사용
    + 스트림의 구성
        - Stream 생성
        - 중개 연산
        - 최종 연산
    + 스트림 생성
        - 배열 스트림
        ```Java
            String[] arr = new String[]{"a","b","c"};
            Stream stream = Arrays.stream(arr);
        ```
        - 컬렉션 스트림
        ```Java
            ArrayList list = new ArrayList(Arrays.asList(1,2,3));
            Stream stream = list.stream();
        ```
    + 스트림 중개연산
        - Filtering - filter 내부 조건에 참인 요소들을 추출
        ```Java
            IntStream intStream = intStream.range(1,10).filter(n->n%2 == 0);
        ```
        - Mapping - map 안의 연산을 요소별로 수행
        ```Java
            IntStream intStream = intStream.range(1,10).map(n->n+1);
        ```
    + 스트림 최종연산
        - 결과를 도출해냄
        ```Java
            IntStream.range(1,5).sum(); // 합
            IntStream.range(1,5).average().getAsDouble(); // 평균

            IntStream.range(1,5).min().getAsInt(); // 최소값
            IntStream.range(1,5).max().getAsInt(); // 최대값
        ```