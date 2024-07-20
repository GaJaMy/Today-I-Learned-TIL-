# DynamoDB
1. DynamoDB란?
    + AWS에서 제공하는 서버리스 기반 Key-Value NoSQL 데이터 베이스

2. 특징
    + NoSQL 데이터 베이스
    + HTTP로 통신
    + 서버리스이다.
    + Key-Value 데이터 베이스이다.
        + 파티션 키와 정렬 키를 기반으로 각 항목들을 구분(RDB의 키와 같은 역할)
        + 파티션 키와 정렬 키를 기준으로 자동 정렬
3. 사용법
    1. 스프링에서 Spring Data DynamoDB
        + 간편하게 사용할 수 있다는 장점이 있지만, 최적화, 커스터 마이징이 되지 않는다.
    2. AWS SDK for Java
        + Spring Data DynamoDB에 비해서 사용하는 방법이 까다롭다.
        + 하지만 최적화 및 커스터 마이징이 가능하다.
        + V1과 V2가 있는데 V2가 더 객체 지향적이므로 적극적으로 V2를 사용
    
    + 엔티티 만들기
    ```java
        @Setter // AWS SDK가 DynamoDB에서 읽어온 항목을 매핑하기 위해서 반드시 필요
        @Builder
        @DynamoDBBean // DynamoDB Entity 라는 걸 알려준다.
        public class User {
            private String id;
            private String name;
            private String phone;

            @DynamoDBPartionKey   // 해당 필드가 파티션 키임을 알려줌
            @DynamoDBAttribute("USER_ID") // 해당 필드가 매핑될 테이블 속성이름을 명시
            public String getId() {
                return id;
            }

            @DynamoDBSortingKey // 해당 필드가 정렬 키임을 알려줌
            @DynamoDBAttribute("USER_NAME") // 해당 필드가 매핑될 테이블 속성이름을 명시
            public String getName() {
                return name;
            }

            @DynamoDBAttribute("USER_PHONE") // 해당 필드가 매핑될 테이블 속성 이름을 명시
            public String getPhone() {
                return phone;
            }
        }
    ```

    + Spring에서 DynamoDB 연동하기(Config 설정)
    ```java
    @Configuration
    public class DyanmodbConfiguration() {
        
    }
    ```