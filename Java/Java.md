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
