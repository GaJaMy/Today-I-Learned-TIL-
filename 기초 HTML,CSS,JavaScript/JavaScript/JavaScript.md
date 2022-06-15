# JavaScript 기초
1. 주석 
    * 기본 주석 남기는 방법은 C++과 같다.
    * JS DOC - 상세하게 이 코드를 사용하는 사람들에게 남기는 주석
        + 최대한 상세하게 적어 줘야 함
***
2. 리터럴
    * 가장 작은 단위의 프로그램에서 나타내는 값
    * 즉, 변수에 들어가는 값 자체를 리터럴이라고 부름
***
3. use strict
    * 기존에 조용히 무시되던 에러들을 throwing 한다.
    * 사용법 해당 기능을 사용하고 싶은 블록이나 가장 최상단에 "use strict;"라고 선언 해준다.
***
4. 타입 시스탬
    * 원시(primitive) 타입 - Stack 메모리 영역에 직접 값이 할당 됨, 재할당시 해당 메모리 영역의 값이
    직접 바뀜(c++의 포인터가 아닌 일반 변수)
        + 예시)
        + var test = false;
        + console.log(!test) ==> true 출력
        + console.log(test) ==> false 출력 : 재할당 되지 않았기 때문에 변하지 않음
        + var test2 = test;
        + test2 = true;
        + console.log(test2) ==> true 출력 : test2를 메모리에 만들고 test의 값을 복사
        + console.log(test) ==> false 출력 : test가 primitive 타입이기 때문에 자체가 변하지는 않음
    * 객체, 참조(referene) 타입 - Heap메모리 영역의 주소 값을 가지고 있음, 만약 재할당 하게 되면 해당 영역의 주소를 바꾸는 것이기 때문에 주의 필요(c++의 포인터와 같다.) 
        + 예시)
        + var test = [1,2,3,4]
        + var test2 = test ==> 객체 타입은 주소를 가리킨다, test2가 test의 주소를 가리키고 있다.
        + test2[0] = 3 =-> 해당 주소에 있는 값을 변경 했다.
        + console.log(test2) ==> [3,2,3,4] 출력 
        + console.log(test) ==> [3,2,3,4] 출력 : test와 test2가 같은 주소를 가리키기 때문에 둘다 변경 된 내용을 반환 한다.
    * 사실 강의 내용으로만 보면 상당히 이해가 안된다. 느낌상으로는 자바스크립트는 모두 call by reference로 이루어 지는 것 같아서 위와 같이 일어나는 것 같다.
***
5. undefined와 null
    + undefined -> 정의되지 않았다.
    + null -> 비어져 있다.
***
6. 타입 변환
    + 명시적 변환 - 직접 변환 하려는 타입으로 Wrapper 객체를 사용하여 변환 시킨다.
    + 암시적 변환 - 직접 변환 하려는 타입을 명시하지 않는다. 해당 변환은 검색 해보자 너무 다양함...
***
7. 비교 연산자
    + == -> 느슨한 비교 그냥 안에 있는 값에 대한 비교만 한다. 즉, 서로 다른 타입을 비교하면 어느 한쪽으로 암시적 형변환이 일어나게 되어 잘못된 결과를 초래할 수 있다.
        - '123' == 123 -> 문자열은 숫자가 아닌데 true가 떨어져 버린다.
    + === -> 엄격한 비교 실제 해당하는 값에 대한 타입까지 비교하기 때문에 안정적이다.
        - '123' == 123 -> 타입이 다르기 때문에 false가 떨어진다.
    + 또한 느슨한 비교는 값들끼리의 비교에도 예상치 못한 결과가 많기 때문에 엄격한 비교를 사용하는게 안전하다.
***
8. Truthy 과 Falsy
    + Truthy - True와 같은 역할을 하는 것 ex) "1" 과 1등 false가 아닌 어떤 값이 있는 것은 True를 반환 한다.
    + Falsy - False와 같은 역할을 하는 것 ex) 대표적으로 숫자 0이 있다. 
    + 왠만하면 true , false를 명확하게 형변환 한 후 사용하자. 편하기야 하겠지만 헷갈린다.   
    + 궁금하면 JavaScript truthy mdn 검색
***
9. Number
    + BigInt - Number 원시 값이 안정적으로 나타낼 수 있는 최대치인 2^53 - 1보다 큰 정수를 표현할 수 있는 <strong>내장 객체</strong>
        - IE에서는 동작하지 않는다.
        - xxxxxxxx...xxxxn 뒤에 n을 붙이는 걸로 표현 가능
        - 19n === 19 => false BigInt와 Number는 완전 동등하지 않다.
    + NaN(Not a number) "숫자가 아니다"라는 걸 나타내는 <strong>내장 객체</strong>
        - Number(undefined) => NaN
        - Number(Null) => NaN
        - Number(true) => 1
        - Number(false) => 0
        - 10 + NaN => NaN
        - 전역 함수 isNaN은 느슨하게 검사 하기 때문에 Number.isNaN을 사용하도록 한다.(Number.isNaN은 ES2015+ 이상에서 부터 사용 가능)
    + Infinity - 너무 크기나 작다를 나타내는 전역 객체
        - Math.pow(2,1024) => Infinity
        - 7/0 => Infinity
        - Infinity 체크는 isFinite(숫자)를 사용
    + ParseInt(변환 하려는 문자, 진수) - 문자를 정수형으로 변환해주는 함수 
***
10. 문자열
    + SingleQueto와 DubleQueto, BackQueto로 만들 수 있는데 BackQueto는 개행 문자까지 표현 가능
    + 문자열 관련 내장 객체 함수는 MDN에서 찾아서 입맛에 맞게 사용
    + Template Literals - BackQueto를 사용하는것 다른 것들을 사용하면 보안상 취약
        - 멀티라인 (개행)이 자유로움
        - Basic String Formatting => 보간법을 활용 => 표현식을 문자열
        - HTML Escaping : 안전하게 사용하도록 도움 (XSS,SQL Injection)

***
11. 배열
    + JavaScript 배열의 length ->  배열의 가장 높은 인덱스를 추적한다. 즉, 진짜로 할당된 배열의 길이를 말하는 것이 아니다. 또한, 배열.length = 3 과 같이 접근해서 바꿀수도 있으니 꼭, 주의하자.
    + 배열을 순회 할시 초기화 변수를 var로 초기화 하면 var의 특성에 따라 전역 변수로 인식 하게 되어 추후에 문제가 생길 수 있으니 반드시 let으로 선언 후 초기화를 하도록 한다.
    + for~of문 for~in 문 =>  객체를 순회할 때 사용하는데, 객체를 접근을 해버리기 때문에 위험하다. 굳이 사용하지 말자
    + 배열의 삽입, 삭제, 검색등은 구글링 해서 찾고 많이 사용 할 것이기 때문에 구글링 ㄱㄱ
    + 유사 배열 객체 - 배열과 유사하지만 배열이 아닌 객체들
        - 배열에서 사용하던 메소드들을 사용할 수 없다.
        - Array.isArray(객체)를 사용하여 진짜 자바스크립트 객체인지 확인 후 사용하자.
***
12. 함수
    + 함수 종류 : 일반 함수, 메소드 함수, 생성자 함수, 화살표 함수
        - 자바스크립트에서는 함수의 리턴형을 생략할 수 있다. 
        - 일반 함수 :  어떤 파라미터를 받아 결과값을 반환한다.
        - 메소드 함수 : 어떤 객체에서 선언되어 있는 함수를 말한다 보통 아래와 같이 만든다.
        ```JavaScript
            const obj = {
                prop: function(){

                }
            }
        ```
        - 생성자 함수 : 특정 인스턴스를 만들어 내는 함수
        ```JavaScript
            //Myobj라는 객체 인스턴스를 만들어 준다.
            function MyObj()
            {

            }
        ```
        - 화살표 함수 : ES2015+,ES6에서 부터 사용가능한 함수 선언 법
        ```JavaScript
            const arrowFunc = () => {}
        ```
    + 함수 표현식
        - 함수 중복 선언과 호이스팅 문제(추후 설명)에 대해서 관리 할 수 있다.
        ```JavaScript
            //기명 함수 표현식
            const func = function func() {
                return 'func'
            }

            //익명 함수 표현식
            const func = function() {
                return 'func'
            }
        ```
    + arguments 객체
        - 가변 인자를 넘길때 사용한다.
        - arguments 객체는  배열이 아니다.
        - 화살표 함수에서는 사용 불가능 하다.
    + rest parameter
        - arguments 객체와 비슷하지만 같지는 않다.
        - 역시 가변인자를 넘길 때 사용한다.
        - 화살표 함수에서도 사용가능
        - 배열이다.
        ```JavaScript
            //arguments 객체와는 다르게 화살표 함수에서도 사용가능하며
            //배열처럼 다룰 수 있다
            const func = (...nums) => {
                return nums.reduce((prev,curr) => prev + curr);
            }

            console.log(func(1,2,3,4,5,6,7)); // 28
        ```
***
13. DOM
    + HTML을 객체로 모델링 하는 것
    + JavaScript언어의 일부가 아님 JavaScript로 HTML을 조작하는것
        - JavaScript에서 문서 자체를 조작하기 위해 모델링 한것을 DOM
    + 여러 조작법은 mdn을 찾아보면서 하다가 익숙해질 수 있도록 한다.
***
14. 이벤트
    + HTML에서 발생한 어떤 행동을 의미 한다. ex) 마우스 클릭
    + 이벤트 다루는 방법
    ```JavaScript
        //reset이라는 클래스 이름을 가진 dom 객체를 가져온다.
        const resetButton = document.querySelector('.reset');
        
        //이벤트 리스너('이벤트 타입',이벤트 발생시 실행할 함수)
        resetButton.addEventListener('click', function () {
            alert('안녕하세요');
        });
    ```
    + 이벤트 객체
        - 해당하는 이벤트를 DOM 객체로 접근 한다.
    ```JavaScript
        //reset이라는 클래스 이름을 가진 dom 객체를 가져온다.
        const resetButton = document.querySelector('.reset');
        
        //이벤트 자체가 발생시 무조건 넘겨준다.
        //마우스의 위치와 같은 것을 알고 싶을 때 event 객체에 접근해서 사용 가능
        resetButton.addEventListener('click', function (evnet) {
            alert('안녕하세요');
        });
    ```
    + 이벤트 핸들러
        - 이벤트를 다루는 메소드 또는 객체 ex)addEventListener메소드
***
15. 객체
    + 객체 생성
        - 싱글 리터럴 방식
        ```JavaScript
            const boject ={
                property : 'value',
                method: function () {},
            }
        ```
        - 생성자 함수 방식, 일반적인 객체를 많이 또는 자주 사용할때 사용
        ```JavaScript
            // 객체 정의
            function NewObejct(name){
                this.name = name;
            }
            //객체 생성
            const newObject = new NewObject('Seo');
        ```
        - 메소드 사용 방식, Object.create 메소드 사용
        ```JavaScript
            const newObject2 = Object.create(
                Object.prototype,
                {
                    name:{
                        value: 'seo'
                        writable: true, //덮어 쓸수 있는지
                        enumerable: true, //열거할 수 있는지
                        configuralbe: true, // 객체 서술자를 수정할 수 있는지
                    },
                },
            );
        ```
    + 프로퍼티 열거
        - forin문을 사용하여 열거한다.
        ```JavaScript
            const obj = {
                prop1 = 'value1',
                prop2 = 'value2',
                prop3 = 'value3',
                prop4 = 'value4',
            };
            
            //for문에서는 const 사용하는게 좋다.
            for(const key in obj){
                //그 자신만의 프로퍼티를 가져 올수 있도록 한다.
                //즉, 상속이 된 프로퍼티는 가져오지 않는다.
                if(obj.hasOwnProperty(key))
                {
                    console.log(obj[key]);
                }
            }
        ```
    + 프로퍼티 조작
        ```JavaScript
            const person{
                firstName: 'seo',
                location: 'korea',
            };

            //추가
            person.lastName = 'hd';
            //수정
            person.lastName = 'hyungdong';
            //삭제
            delete person.location;
        ```
    + 프로퍼티 접근자
        - getter,setter 안전하게 객체의 프로퍼티에 접근하기 위한 접근자
        ```JavaScript
            const person{
                _firstName: 'seo',
                location: 'korea',
                
                //해당 프로퍼티를 가져온다.
                get firstName(){
                    return this._firstName;
                }

                //해당 프로퍼티를 파라미터로 받아온 것으로 바꿔준다.
                set fristName(newFirstName){
                    if(tyoeif newFirstName === 'string'){
                        this._firstName = newFirstName;                
                    }
                    else 
                        return;
                }
            };
        ```
    + 인스턴스
        - 클래스나 프로토타입을 사용하여 만들어 낸 것, 실제 고유한 객체
