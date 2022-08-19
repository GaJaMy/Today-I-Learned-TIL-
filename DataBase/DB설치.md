# MariaDB
1. Windows
    1. [마리아 DB 사이트 접속](https://mariadb.org/) 후 다운로드 페이지 접속
    2. MariaDB Server 선택 후 아래 옵션을 잘 선택해서 설치
        - 220805기준 안정된 버전은 10.6.8
        - 버전, OS환경, 타입 설정후 다운로드
    3. 원하는 위치에 해당 폴더 압축을 풀어 줌
    4. 관리자 창에서 bin 폴더로 이동 후 아래와 같은 작업을 해준다.
    ```
        mariadb-install-db -S MariaDB : 윈도우 서비스 등록
        mariadb-install-db -p root : 관리자 비밀번호 설정(나만 알 수 있게)
    ```
    5. 그러면 DB 생성이 완료 된다.
    6. db 서비스 시작/중지/상태확인 명령어
    ```
        sc start 서비스 명
        sc stop 서비스 명
        sc query 서비스 명
    ```
    7. db 접속 mysql을 통해 접속(일반 명령 프롬프트로)
        + 원격 서버에 접속을 하기 위한 정보
            - IP 주소
            - 포트 번호(3306)
            - 계정
            - 비밀번호
            - 인스턴스
        + 로컬에서 접속할때
            - 127.0.0.1,또는 localhost
            - 포트 번호(3306)
            - 계정
            - 비밀번호
            - 인스턴스
        ```
            mysql -h 호스트IP -u 유저 -p 
        ```
    8. 접속 완료되먄 설치 끝
2. 리눅스 환경 - 다시 강의 보고 해보는 걸 추천
    1. 먼저 원격 접속을 위해 ssh를 설치해야함
        - sudo apt install openssh-server : openssh 설치 명령어
    2. 윈도우즈의 경우 푸티나 xshell같은걸로 접속
    3. 해당 리눅스 버전을 확인한다
        - cat /etc/*release* 했을때, ID와 RELEASE, CODENAME확인
    4. MariaDB.org접속 후 MariaDB Repositoriese 선택 후 3에서 확인한 환경과 버전을 선택
    5. 원하는 MariaDB버전 선택
    6. 그 아래 나오는 순서대로 설치 진행
3. 직접 리눅스 서버에 있는 마리아 DB 접속
    1. 먼저 Xshell 또는 푸티를 통해 해당 데이터 베이스 서버에 접속
        - putty 이용방법은 [여기](../Computer%20Science/System%20Softeware/SystemSoftware.md)참고 Xshell은 [여기](https://itmoon.tistory.com/58) 참고
    2. 관리자 권한으로 전환
        - sudo -i 입력 후 패스워드 입력
    3. 설정 파일을 찾기
        - find / -name my.cnf 그러면 /etc/mysql/my.cnf를 찾을 수 있음
        - my.cnf 파일이 mysql 설정 파일 인듯
    4. vi로 my.cnf파일을 열어 아래 내용 추가
        ```
            [mysqld]
            bind-address=0.0.0.0
        ```
        - 추가로 aws에 데이터 베이스 직접 연결시에는 인스턴스의 inboud 규칙을 재설정 해 줘야함[여기 참고](https://conkjh032.tistory.com/28)
    5. 외부에서 접속할 계정을 추가해야함
        - 먼저 서버에 계정 정보 확인 select Host, User, Password from user로 확인 하면 전부다 Host가 localhost로 되어 있음
        - 추가 방법
        ```
            create user 'root'@'%' identified by 'hyungdong'; : 계정 생성
            grant all privileges on*.* to 'root'@'%' identified by 'hyungdong'; : hyungdong라는 비밀번호로 들어오는 계정에 모든 권한을 주겠다.
            flush privileges : 계정 정보 업데이트
        ```
4. 데이터 베이스 툴
    - DataGrip 