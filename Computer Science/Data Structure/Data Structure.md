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