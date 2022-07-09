# 컴퓨터 구조
1. 컴퓨터 시스템
    + 하드웨어 : CPU,Memory,Storage,Network 등
    + 소프트웨어 : 운영체제와 응용 프로그램
***
2. 폰노이만 구조
    + Memory에 프로그램과 데이터가 저장
    + 하나씩 꺼내어 CPU:Arithmetic Logic Unit(ALU)로 연산
***
3. 컴퓨터 주요 구성 요소
    + CPU(중앙처리장치,Central Processor Unit)
        - 연산: ALU(Arithmetic Logic Unit)
            - 산술 연산 : Arithmetic Operation
            - 논리 연산 : Logic Operation
        - 제어: Control Device
            - IO Device, Memory, ALU 동작을 제어
    + Memory(코드와 데이터를 저장하는 장치)
        - 프로그램과 프로그램 수행에 필요한 데이터를 저장
        - 내부 기억장치(주기억장치)
            - CPU 안에 레지스터(register), 캐쉬(cache memory)
            - DRAM등 메모리(램, RAM, DDR4)
        - 외부 기억장치(보조기억장치)
            - SSD,HDD
    + IO Devices(입출력 장치)
        - 입력 장치 : 마우스, 키보드, 터치패드등
        - 출력 장치 : 모니터, 프린터, 스피커등
    + Bus(버스)
        - CPU, Memory, IO Devices를 연결해주는 장치
        - 개별적인 IO Devices들을 연결해주고 데이터를 송수신 해주는 장치
***
4. 논리 게이트 - 불 대수를 물리적 장치에 구현한 것으로, 하나 이상의 논리적 입력값에 대해 논리 연산을 수행하여 하나의 논리적 출력값을 얻는 전자회로
    + AND 게이트 - 둘다 1일 경우에만 1이 출력
    + OR 게이트 - 어느 하나라도 1일 경우에 1이 출력
    + NOT 게이트 - 1은 0, 0은 1로 반전시키는 게이트
    + NAND 게이트 - AND 게이트에 NOT게이트를 붙인 것
    + NOR 게이트 - OR 게이트에 NOT 게이트를 붙인 것
    + XOR 게이트 - OR 게이트와 NAND 게이트에 AND 게이트를 붙인 것
***
5. 가산기와 클럭 동작
    + 반가산기(half adder) - 두 bit를 덧셈하는 가산기를 의미
        - 두 비트 A + B를 더해서 합 S와 자리 올림(Carry) C를 출력하는 조합 논리 회로
    + 전가산기(full adder) - 두 bit 외, carry 까지 계산할 수 있는 가산기
        - 반가산기를 조합해서 전가산기를 만들 수 있다.
        - 전가산기는 두 비트(A, B)와 Carry 비트 값을 입력 받아 sum과 carry값을 출력해 줌
    + 클럭 - 컴퓨터가 작은 시점마다 동작하는 것 결국 명령 집합 처리 단위
    + 발진기(Oscillator) - 0과 1을 반복하는 것 =>  이것을 이용하여 클럭을 만들수 있다.
        - 주기(Cycle) - 1->0->1 한번을 1 사이클 즉, 이 사이클 마다 명령 하나
        - frequency -> 1초에 발생하는 사이클단위는 Hz
        - ex) 2.5GHz -> 2.5*1000000000번의 사이클이 생긴다. 
***
6. 플리플롭(flip-flop)과 메모리
    + 메모리는 플리플롭이란 논리 회로로 이루어져 있다.
    + 플리플롭(flip-flop) - NOR 게이트를 조합해서 만들어진 논리 회로, 데이터를 저장하는 조합 논리 회로
        - RS 플리플롭(Reset Set Flip-Flop)
        - Level-triggered flip-flop - Hold That Bit라는 것을 RS 플리플롭에 AND 게이트로 연결 해준 것
        - D-type flip-flop(Level-triggered D-type flip-flop) - Level-triggered 플리플롭에 Reset과 set을 하나의 input으로 만든것 -> 이게 곧 메모리의 기능을 한다.
***
7. 메모리 동작
    + 1bit latch - Level-triggered D-type flip-flop 하나를 나타냄
        - Hold That Bit를 Write로 표시
        - Data In이 데이터를 넣을 때, Write도 1이 되어야함
        - 1bit를 일시적으로 저장할 수 있는 메모리
    + 8bit latch - 8개의 latch를 연결
        - 3-to-8 decoder와 8-to-1 selector로 구성 -> Address를 나타낸다.
    + 8-to-1 selector
        - 8 bit 데이터에서 특정 bit 값만 출력하는 회로
    + 3-to-8 decoder
        - 8개의 출력 중 , V 스위치로 단 하나만 선택하는 회로 즉, 8-to-1의 반대라고 보면 됨
    + RAM(Random Access Memory)
        - 온전한 8bit latch 회로
        - 3 Address가 있으면 8개중 하나의 비트를 선택해서 쓰거나 읽을 수 있다.
        - 특징1 - Memory : 데이터를 저장 할 수 있음
        - 특징2 - Read/Write : 특정 공간에 새로운 데이터 저장, 읽기 가능
        - 특징3 - Random Access : Address 지정을 통해 틀정 공간 값 접근 가능
            - 반면 어떤 메모리는 순차 접근만 가능하기도 하다.
    + RAM array
        - 8 X 1 RAM을 여러게 이어 붙인 것