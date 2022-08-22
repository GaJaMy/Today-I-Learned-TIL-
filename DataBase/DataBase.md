# 데이터 베이스
1. 데이터, 정보, 지식
    - 데이터 : 관찰의 결과로 나타난 정량적 혹은 정성적인 실제 값
    - 정보 : 데이터의 의미를 부여한 것
    - 지식 : 사물이나 현상에 대한 이해
    - 지혜 : 지식을 기반으로 도출되는 창의적인 아이디어
2. 파일 시스템 
    - 컴퓨터에서 파일이나 자료를 쉽게 발견 및 접근할 수 있도록 보관 또는 조직하는 체계
    - 운영체제가 저장되어 있는 파일이나 폴더들을 효율적으로 관리 및 읽고 쓰는 방법을 말함
    - 단점
        - 데이터 중복성
            - 데이터 불일치
            - 같은 데이터에 동일한 보안수준 적용이 불가
            - 저장공간 낭비 및 갱신에 대한 비용
            - 데이터의 정확성 확보 불가
        - 데이터 종속성
            - 응용 프로그램에 종속적(예를 들어, 구조, 접근방법이 변경되면 프로그램도 같이 변경해야 함)
    - 장점
        - 처리 속도가 빠름
        - 구현이 간편
        - 비용이 저렴
3. 데이터 베이스 특징
    - 실시간 접근성 : 데이터 베이스는 실시간으로 서비스, 사용자가 데이터를 요청하면 몇 시간이나 몇 일 뒤에 결과를 전송하는 것이 아니라 수 초 내에 결과를 서비스
    - 계속적인 변화 : 데이터 베이스에 저장된 내요은 어느 한 순간의 상태를 나타내지만, 데이터 값은 시간에 따라 항상 바뀜, 데이터 베이스는 삽입,삭제,수정 등의 작업을 통하여 바뀐 데이터 값을 저장
    - 동시공유 : 데이터 베이스는 서로 다른 업무 또는 여러 사용자에게 동시에 공유된다, 데이터 베이스에 접근하는 프로그램이 여러개 있다.
    - 내용에 따른 참조 : 데이터베이스에 저장된 데이터는 데이터의 물리적인 위치가 아니라 데이터 값에 따라 참조, 사용자가 원하는 데이터를 얻기위해 데이터 값을 사용하여 조건을 제시하면 데이터베이스는 이에 해당하는 데이터를 검색해 준다.
    - 데이터 독립성 : 응용프로그램과 데이터베이스를 독립데이터의 논리적 구조를 변경시켜도 응용프로그램은 변하지 않음
4. 데이터 베이스 관리 시스템
    + DBMS은 데이터 베이스를 체계적으로 관리하기 위한 시스템
        - 유형
            - 계층형 - 데이터 베이스 내의 데이터의 구조가 상하 종속적 관계로 계층적으로 구성되어 있다.
                - 엑세스 속도가 빠르며 데이터 사용량을 쉽게 예측할 수 있다.
                - 데이터 종속성 때문에 유지보수가 어렵다.
                - 일반적으로 거의 사용 안됨
            - 망(네트워크)형 - 데이터베이스의 구조를 네트워크 강의 노드 형태로 표현
                - 설계가 복잡하고 데이터 종속적 문제가 있음 
            - 관계형 - 수학적 논리 관계를 테이블 형태로 구성한 구조, 테이블 내의 컬럼중 일부를 다른 테이블과 중복함으로 각 테이블간의 상관관계를 정의
                - Data사이의 동력인 상호관계는 조인을 통하여 일어난다. 성능 문제가 중요하게 발생함
                - 데이터베이스 스키마에 대한 동적인 변화들이 가능하다.
                - 레코드들을 중복시킴으로서 성능 함정에 빠질 수 있다.
            - 객체지향형 - 복잡한 데이터 구조를 표현 할 수 있는 데이터 베이스 관리 시스템 
            - 객체관계형 - DBMS의 안정된 성능에 기반하면서 OODBMS의 장점을 도입한 시스템
    + RDBMS
        - 하나의 테이블은 행과 열로 구성
            - 열 : 필드, 속성이라고도 
            - 행 : 튜플, 레코드
5. 데이터베이스 인스턴스
    + 데이터 베이스 보기 
    ```sql
        show databases;
    ```
    + 생성/삭제
        - DBMS랑도 대화하기 위해서는 언어가 필요
        - 이 언어를 SQL 이라고함
        - 목적: DBMS의 데이터를 관리하기 위해서 설계된 특수 목적의 프로그래밍 언어
        - SQL 명령어
            - 데이터 정의어 - 테이블과 인덱스 구조를 관리
            - 데이터 조작어 - 데이터를 조작
            - 데이터 제어어 - 권한을 주는 등의 데이터를 제어
        - 생성/삭제
            - CREATE DATABASE{데이터 베이스 인스턴스 명};
            - DROP DATABASE{데이터 베이스 인스턴스 명};
        ```sql
            //생성
            create database db1;
            //삭제
            drop database db1;
        ```
    + 계정 생성
        - CREATE USER '생성할 계정 명'@'도메인(localhost,%)' IDENTIFIED BY '패스워드'
            - localhost는 외부에서 접속을 막는다.
    + 계정 권한 설정
        - GRANT ALL PRIVILEGES ON 권한을 주고 싶은 DB.* TO '계정명'@'도메인' IDENTIFIED BY '패스워드'
6. 테이블
    + 데이터는 관계형 데이터 베이스의 기본 단위인 테이블 형태로 저장
    + 모든 자료는 테이블에 등록되고, 테이블로부터 원하는 자료를 꺼내옴
    + 테이블은 어느 특정한 주제와 목적으로 만들어지는 일종의 집합
    + 새로운 데이터를 입력할 때, 새로운 테이블을 생성할 필요 없이 데이터만 추가하면 됨
    + 데이터 자료형
        - 숫자 데이터
        - 문자열 데이터
        - boolean 데이터
        - 날짜/시간 데이터
        - 큰 객체 데이터
    + 테이블 생성
    ```sql
        create 테이블 명(
            속성1,
            속성2,
            속성3,
            ...
        );
    ```
7. CRUD
    + 컴퓨터 소프트웨어가 가진 기본적인 데이터 처리 기능
        - Crete - 생성 -> insert
        - Read - 읽기 -> select
        - Update - 갱신 -> update
        - Delete - 삭제 -> delete
8. 데이터 처리
    + INSERT - 데이터 추가
        ```sql
            insert into 테이블명(컬럼1,컬럼2,...)values(값1,값2,값3..);
        ```
        - 하나의 레코드(튜플, 행)을 구분할 수 있어야 함
        - PK(primary key) : 주 식별자 키로 테이블의 모든 데이터를 식별하는 컬럼
        - 특징: 중복 불가, null 불가
        - FK(foreign key) : 외부 식별자 키로 테이블 간의 종속 관계
        ```sql
            --프라이머리 키 설정--
            --alter : 테이블의 내용을 수정하는 명령어--
            alter table 테이블 명
            add constraint primary key pk_zerobase_memer (컬럼);

            --프라이머리 키 해제--
            alter table 테이블명
            drop primary key;
        ```
    + UPDATE - 갱신
        ```sql
            update 테이블 명
            set
                컬럼1 = 값1,
                컬럼2 = 값2,
                ....
            where 조건;            
        ```
    + DELETE - 데이터 삭제
        ```sql
            delete
            from 테이블명
            where 조건;
        ```
    + SELECT - 데이터 조회
        ```sql
            select 출력 대상 컬럼명1, 출력 대상 컬럼명2...
            from 출력 대상 컬럼들이 있는 테이블 명
            where 조건;
        ```
9. alias와 *(에스터리스크)
    + 일종의 별명이라고 생각하면 됨
        - 테이블에 대한 별명 : 테이블명 바로 뒤에 옴
        - 컬럼에 대한 별명 : 컬럼 바로 뒤에 옴
        - as키워드를 이용해서 사용(옵션사항)
    ```sql
        select 테이블 별명.컬럼 as 컬럼 별명1,
            테이블 별명.컬럼 as 컬럼 별명2
            ...
        from 테이블 명 as 테이블 별명;
    ```
    + *는 모든 컬럼을 말함
10. JOIN
    + 여러 테이블을 함께 조회하기
        - 2개 이상의 태이블을 연결해 데이터를 출력하는 것
        - 일반적으로 사용되는 sql문장의 상단수가 조인을 이용
        - join은 관계형 데이터 베이스의 가장 큰 장점이면서 핵심 기능
        - 일반적인 경우 행들은 PK나 FK값의 연관에 의해 조인이 성립(하지만, 특별한 경우에는 PK나 FK의 관계가 없어도 논리적인 값들의 연관만으로 조인 가능)
        - JOIN은 2개의 테이블을 JOIN하지만, 여러개의 테이블이 있더라도 JOIN이 가능함(먼저 특정 2개의 테이블만 JOIN처리하고, 그 결과 집합을 다시 나머지 테이블과 JOIN. 다만, 이 때 JOIN 순서는 내부적으로 DBMS 옵티마이저가 결정)
        ```sql
            -- inner join --
            select m.member_type,m.user_id,m.password,m.name,
                    md.mobile_no,md.marketing_yn,md.register_date
            from member as m
                join member_detail as md
                    on m.member_type = md.member_type and m.user_id = md.user_id
            ;

            -- left join --
            select m.member_type,m.user_id,m.password,m.name,
                   md.mobile_no,md.marketing_yn,md.register_date
            from member as m
                left join member_detail as md
                    on m.member_type = md.member_type and m.user_id = md.user_id
            ;

            -- right join --
            select m.member_type,m.user_id,m.password,m.name,
                   md.mobile_no,md.marketing_yn,md.register_date
            from member as m
                right join member_detail as md
                    on m.member_type = md.member_type and m.user_id = md.user_id
            ;

            -- full join --
            select m.member_type,m.user_id,m.password,m.name,
                   md.mobile_no,md.marketing_yn,md.register_date
            from member m
                join member_detail md;
        ```
11. CASE~END 문 -> 자바에서 if문과 같다.
    ```sql
        case 컬럼
            when 조건1 then 값1
            when 조건2 then 값2
            else 값3
        end
    ```

12. DBMS 내장 함수 -> 검색해서 사용할 것
    + 벤더에서 기본적으로 제공하는 내장 함수
    + 대부분의 데이터베이스에서 제공하는 함수들
    + 단일행 함수 : 함수의 입력값이 단일행 값이 입력
    + 다중행 함수 : 함수의 입력값이 여러행 값이 입력 -> 집계 함수, 그룹 함수 등...

    + 문자형 함수 : 문자를 입력하면 문자난 숫자 값을 반환
    + 숫자형 함수 : 숫자를 입력하면 숫자 값을 반환
    + 날짜형 함수 : date 타입의 값을 연산
    + 변환형 함수 : 문자, 숫자, 날짜형 값의 데이터 타입을 변환
    + NULL 관련 함수 : NULL을 처리하기 위한 함수
    ```sql
        --문자열 관련--
        concat(문자열1,문자열2) --문자열 합치기 문자열1문자열2--
        substring(컬럼명,1,2 )  --문자열 자르기 컬럼명에 있는 데이터를 1번 위치에서 2개만큼 자른다--
        trim(문자열) -- 문자열 좌우 공백 제거--
        length(password) --문자열 크기 출력--

        --날짜 관련--
        date_format(컬럼명,포멧(검색)) --날짜 -> 문자열--
        str_to_date(문자열,포멧(검색)) --문자열 -> 날짜--
        date_add(날짜,interval 1 주기) --날짜 더하기--
    ```

13. 페이징 처리
    + 데이터는 엄청 많아 한번에 모두 보는데 한계가 있음
    + 특정 위치부터 특정 개수까지 내려줌
    + mysql,mariadb의 경우 limit를 이용하여 페이징 처리 가능
    + oracle의 경우 rownum을 이용해서 페이징 처리가능
    + mssql의 경우 offset,fetch를 이용해서 처리(2012이후)

14. VIEW 테이블
    + 실질적으로 존재하는 테이블이 아니라 정의만 가지고 있음
    + 가상 READ ONLY 테이블
    + 사용에 있어서, 테이블과 동일 (단, SELECT에서만)
    + 장점
        - 독립성 : 테이블 구조가 변경되어도 뷰를 사용하는 응용프로그램은 변경하지 않아도 된다.
        - 편리성 : 복잡한 쿼리를 뷰로 생성함으로써 관련 질의를 단순하게 작성할 수 있음
        - 보안성 : 권한에 따로 표시하지 않아야 하는 컬럼의 경우 숨길 수 있음
    ```sql
        --뷰 생성--
        create view 뷰테이블 명 as 
            쿼리 작성
        ;

        --뷰 삭제--
        drop view 뷰테이블 명;
    ```

15. 함수
    + DBMS에서 제공되는 공통적 함수 이외에 사용자가 직접 정의하고 작성
    + SQL을 활용하여 일련의 로직을 수행하고, 수행 결과를 단일 값으로 반환할 수 있는 모듈
    + 그냥 복잡하고 반복적인 내용의 쿼리를 간단히 하여 사용
    ```sql
        create function 함수명(파라미터)
            returns 반환할 데이터 타입
        begin
            수행할 쿼리
            return 반환할 값
        end;
    ```

16. 프로시저
    + 여러개의 sql문을 같이 실행
    + 데이터 베이스에 대한 일련의 작업을 정리한 절차를 관계형 데이터베이스 관리 시스템에 저장한 것으로 영구저장모듈 이라고도 불림
    + 함수와 비슷하나 다름 [프로시저와 함수의 차이] (https://fomaios.tistory.com/entry/Oracle-%ED%95%A8%EC%88%98Function%EC%99%80-%ED%94%84%EB%A1%9C%EC%8B%9C%EC%A0%80Procedure-%EC%B0%A8%EC%9D%B4)
    ```sql
        create procedure 프로시저명(파라미터)  -- 함수와는 다르게 파라미터에 입력--
        begin                                 -- 이 여러게 입력 출력 입출력이 옴--
            쿼리문
        end; 
    ```

    + delimiter 
        - 문법의 끝을 나타내는 역할을 함
        - 콘솔 작업을 할 때 ; 을 만나면 문장이 끝났다고 인식 하기 때문에 다시 문장의 시작과 끝을 나타내기 위해 사용
        ```sql
            delimiter $$
                프로시저
            delimiter; 
        ```

17. 트리거
    + 트리거란 특정 조건이 만족하면 저절로 실행되는 일종의 장치
    + 프로시저나 함수와 다르게 한번 설정을 하면 동작을 감시하고 있다가 조건에 해당하는 동작이 수행되는 순간 실행되는 특징
    + 주로 insert,update,delete 동작에 사용
    + 동작 시점은 after,before가 있음
        - insert -> old : 해당사항 없음 , new o
        - update -> old : o , new o
        - delete -> old : o , new 해당사항 없음
    ```sql
        create trigger 트리거 명
            before update on 테이블 명 -- update가 발생한 테이블 --
            반복
            begin
            커리
            end
    ```

18. 추가
    + like '%문자열%' : 뒤의 문자열에서 %의 문자열 기준 %방향으로 어떤 문자든 붙어있는걸 검색 