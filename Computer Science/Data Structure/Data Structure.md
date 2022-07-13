# 자료구조
1. 자료구조- 자료를 효율적으로 관리하기 위한 구조, 목적에 맞게 사용한 좋은 자료구조는 실행시간 단축, 메모리 용량 절감 효과가 있음
    + 선형 자료구조
        - 배열
        - 연결리스트
        - 스택, 큐, 데크
        - 해시 테이블
    + 비선형 자료구조
        - 트리
        - 그래프
        - 힙/우선순위 큐
        - 트라이
***
2. 선형 자료구조
    + 배열(Array) - 많은 수의 데이터를 다룰 때 사용하는 자료구조,각 데이터를 인덱스와 1:1대응 하도록 구성, 데이터가 메모리 상에 연속적으로 저장
        + 장점
            - 인덱스를 이용하여 데이터에 빠르게 접근 가능
        + 단점
            - 데이터의 추가/삭제가 번거롭다.
                - 미리 최대 길이를 정해서 생성해야함
                - 가변 길이 배열은 배열의 크기를 변경할 때마다 새로운 배열을 생성
                - 데이터 삭제 시, 인덱스를 유지하기 위해 빈 공간 유지
    + 연결 리스트(Linked List) - 데이터를 링크로 연결해서 관리하는 자료구조 , 자료의 순서는 정해져 있지만, 메모리상 연속성이 보장되지는 않음
        + 장점
            - 데이터 공간을 미리 할당할 필요 없음
            - 즉, 리스트의 길이가 가변적이라 데이터 추가/삭제 용이
        + 단점
            - 연결 구조를 위한 별도 데이터 공간 필요
            - 연결 정보를 찾는 시간이 필요(접근 속도가 상대적으로 느림)
            - 데이터 추가, 삭제 시 앞뒤 데이터의 연결을 재구성하는 작업 필요
        + 연결 리스트 기본 구조
            - 노드 - 데이터 저장 단위로, 값과 포인터(다음 노드나 이전 노드의 연결 정보)로 구성
            - 데이터 추가 - 마지막에 추가 될 때
                1. 추가할 데이터를 담을 노드 생성
                2. 링크 연결 작업
                3. head 이전 작업
            - 데이터 추가 - 중간에 추가 될 때
                1. 추가할 데이터를 담을 노드 생성
                2. head로 부터 데이터 추가 위치 직전 노드까지 순회
                3. 링크 연결 작업
            - 데이터 삭제 - 가장 앞의 데이터 삭제 시
                1. 삭제 대상 노드 지정(delete_node)
                2. head 이전 작업
                3. delete_node 삭제 -> 그냥 head를 바꿔주면 된다.
            - 데이터 삭제 - 중간 데이터 삭제 시
                1. head로 부터 삭제 대상 노드까지 순회 및 해당 노드 지정(delete_node)
                2. 삭제 대상 이전/이후 노드의 링크 연결 작업
                3. delete_node 삭제 -> 그냥 head를 바꿔주면 된다.
            - 리스트의 종류
                - 단순 연결 리스트 - 각 노드가 단순히 다음 노드만을 가리키고 있으며, 헤드 하나만 있다.
                ```Java
                    class Node{
                        int data;
                        Node next;
                    }
                    class LinkedList {
                        Node head;
                    }
                ```
                - 양방향 연결 리스트 - 각 노드가 prev와 next로 이전 노드와 다음 노드를 가리키며, head와 tail 이라는 리스트의 양쪽 끝을 가리키고 있다.
                ```Java
                    class Node{
                        int data;
                        Node prev;
                        Node next;
                    }
                    class DoubleLinkedList {
                        Node head;
                        Noda tail;
                    }
                ```
                - 원형 연결 리스트 - 모양 자체는 양방향 연결 리스트와 같으나, head가 다시 tail을 가리키고 tail이 다시 head를 가리킨다.
                ```Java
                    class Node{
                        int data;
                        Node prev;
                        Node next;
                    }
                    class CircularLinkedList {
                        Node head;
                        Noda tail;

                        CircularLinkedList(Node node){
                            this.head = node;
                            this.tail = node;
                            node.next = this.head;
                            node.prev = this.tail;
                            // 하나만 있을 경우 다시 자기 자신을 가리키며 순환한다.
                        }
                    }
                ```
    + 스택(Stack) - 후입선출(Last In First Out : LIFO), 마지막에 들어온 데이터가 먼저 나가는 구조, 데이터가 입력된 순서의 역순으로 처리되어야 할 때 사용
        ```Java
            //스텍의 생성
            Stack stack = new Stack();

            //push - 뒤에 삽입
            stack.push(1); // 1
            stack.push(2); // 1 2

            //pop - 뒤에 있는 데이터를 꺼냄
            stack.pop() // 1
            
            //peek - 가장 마지막에 있는 데이터를 리턴하지만 데이터를 꺼내진 않음
            stack.peek() // 1

            //contain - 해당 데이터가 있는지 검색
            stack.contains(1) // true;

            //size - 스텍의 크기 출력
            stack.size(); // 1 
        ```
    + 큐(queue) - 선입선출(First In First Out : FIFO), 먼저 들어온 데이터가 먼저 나가는 구조, 입력 순서대로 데이터 처리가 필요할 때 사용
        - 데이터 추가(Enqueue)
        - 데이터 꺼내기(Dequeue)
        ```Java
            // Queue는 인터페이스로 되어있어서 new를 사용해 바로 사용 할 수 없다. 왜냐하면 다 오버라이딩 해줘야 하기 때문
            Queue queue = new LinkedList();

            //큐에 삽입
            queue.add(1);
            queue.add(2);
            queue.add(3);
            queue.add(4);
            queue.add(5);
            System.out.println(queue);              //1,2,3,4,5

            //Dequeue
            System.out.println(queue.poll());       //1    
            System.out.println(queue);              //2,3,4,5

            System.out.println(queue.poll());       //2
            System.out.println(queue);              //3,4,5

            //꺼내진 않고 리턴만 해줌
            System.out.println(queue.peek());       //3
            System.out.println(queue);              //3,4,5

            //데이터 검색
            System.out.println(queue.contains(3));  //true
            //큐 크기
            System.out.println(queue.size());       //3
            //큐가 비었는지 검사
            System.out.println(queue.isEmpty());    //false

            //큐 비우기
            queue.clear();                          //
        ```
    + 데크(deque) - 양쪽에서 삽이과 삭제가 모두 가능한 자료구조 (Doubly-ended Queue), Stack과 Queue를 합친 형태이다.
        - 기본 구조는 양방향에서 삽입 삭제가 가능 한 구조이다.
        - 일부 기능을 제한하여 용도에 맞게 변형 가능 (어느 한쪽의 출력이나 입력을 제한함으로써 가능)
        - add(offer) : 삽입
        - remove(poll) : 삭제
        ```Java
            Deque deque = new ArrayDeque();

            // Front 부분 입력
            deque.addFirst(1); 
            deque.addFirst(2);
            deque.addFirst(3);
            System.out.println(deque);          //3,2,1

            // Rear 부분 입력
            deque.addLast(10);
            deque.addLast(20);
            deque.addLast(30);
            System.out.println(deque);          //3,2,1,10,20,30

            // Front 부분 출력
            System.out.println(deque.removeFirst());//3
            System.out.println(deque);          //2,1,10,20,30
            // Rear 부분 출력
            System.out.println(deque.removeLast());//30
            System.out.println(deque);          //2,1,10,20

            // 주의 removeLast는 만약 데크가 비어 있는 경우 예외 발생
            // pollLast를 쓰면 데크가 비어 있는 경우 null을 반환 시켜줌 따라서, 어떤 예외 처리 가능
            System.out.println(deque.removeLast());
            System.out.println(deque.removeLast());
            System.out.println(deque.removeLast());
            System.out.println(deque.removeLast());
            System.out.println(deque.pollLast());
            System.out.println(deque);//[]
        ```
    + 해시 테이블(Hash Table) - 해시 맵, 해시 표라고도 하며, 키와 값을 대응시켜 저장하는 데이터 구조이다, 키를 통해 해당 데이터에 빠르게 접근이 가능 하다.
        - 해싱 - 키를 특정 계산식에 넣어 나온 결과를 사용하여 값에 접근하는 과정
        - 키 - 해시 테이블 접근을 위한 입력 값
        - 해시 함수 - 키를 해시 값으로 매핑하는 연산
        - 해시 값 - 해시 테이블의 인덱스
        - 해시 테이블 :  키-값을 연관시켜 저장 하는 데이터 구조
        - 해시 충돌 -  해시 테이블의 같은 공간에 서로 다른 값을 저장하려는 경우
            - 서로 다른 키의 해시 함수를 통한 해시 값이 동일한 경우
            - 해결 방법
                - 개방 주소법(Open Address)
                    - 충돌 시, 테이블에서 비어 있는 공간의 hash를 찾아 데이터를 저장
                    - hash와 value가 1:1 관계 유지
                    - 비어 있는 공간 탐색 방법에 따라 분류
                        - 선형 탐사법(Linear Probing) - 간단히 그냥 해시 함수를 통해 나온 공간이 이미 사용중이면 바로 비어있는걸 찾아서 넣어준다
                            - 빈 공간을 순차적으로 탐사하는 방법
                                - 충돌 발생 지점 부터 이후의 빈 공간을 순서대로 탐사
                            - 일차 군집화 문제 발생
                                - 반복된 충돌 발생 시 해당 지점 주변에 데이터가 몰리는 경우 발생
                        - 제곱 탐사법(Quadratic Proding) -  비어있는 공간을 2의 n승 씩 이동하면서 비어 있는 걸 찾아 할당
                            - 빈 공간을 n제곱만큼의 간격을 두고 탐사하는 방법
                                - 충돌 발생 지점 부터 이후의 빈 공간을 n제곱 간격으로 탐사
                            - 일차 군집화 문제 일부 보완
                            - 이차 군집화 문제 발생 가능성
                        - 이중 해싱(Double Hashing) - 그냥 빈공간을 찾아 다니는게 아니라 해시 함수 자체를 한개를 더 써버리자는 개념
                            - 해싱 함수를 이중으로 사용
                                - 해시 함수 1: 최초 해시를 구할 때 사용
                                - 해시 함수 2: 충돌 발생 시, 탐사 이동 간격을 구할 때 사용
                            - 선형 탐사, 제곱 탐사에 비해 데이터가 골고루 분포됨
                - 분리 연결법(Separated Chaining) - 테이블의 빈공간을 찾아 가는게 아니라 연결리스트를 통해서 해당 하는 테이블이 쭉쭉 늘어감, 때문에 해당 데이터를 get하는 경우 더 오랜 시간(BigO(n))이 걸림
                    - 해시 테이블을 연결 리스트로 구성
                    - 충돌 발생 시, 테이블 내의 다른 위치를 탐색하는 것이 아닌 연결 리스트를 이용하여 해당 테이블에 데이터를 연결
                    