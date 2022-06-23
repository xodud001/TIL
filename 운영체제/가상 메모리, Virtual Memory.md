# 가상메모리
* 프로그램이 물리적 메모리의 크기보다 더 클 경우에, 메모리에 완전히 다 올리지 않은 프로세스도 excution 할 수 있는 technique

* 메인 메모리를 매우 큰 array of storage로 추상화 하면, 물리적 메모리로부터 논리적 메모리를 separate 할 수 있다.

* sharing files and libraries 와 process creation에 효율적인 메커니즘을 제공

## Virtual Address Space
* 프로세스가 메모리에 저장되는 방식에 대한 logical view
* 일반적으로, logical address는 0으로 시작한다

## Virtual Memory
* 2개의 프로세스에서 page sharing을 통해 파일과 메모리 가 공유될 수 있다

## 프로그램이 실행되는 방법
1. secondary storage로에서 memory로 load
* 물리적 메모리에 전체 프로그램을 전부 로드하는 방식
* demand paging은 위 상황을 대체하는 전략
    * page를 필요할 때만 load
    * 일반적인 virtual memory 시스템에서 사용
* demand-paged virtual memory에서 실행되는 동안 demand 될 때 page가 load 된다

## Basic Concepts of the Demand Paging
* 프로세스가 실행중일 때, some page는 in memory에, 또 다른 page는 in secondary storage에 있다
* 위 두가지 상황을 구분하기 위해 `valid-invalid bit scheme`가 사용된다.
    * valid: the page is both legal and in memory
    * invalid: the either is not valid or currently in secondary storage
    
## The procedure for handling the Page Fault
1. 프로세스의 internal table을 확인하여 참조가 valid인지 invalid memory access인지 determine하기 위해 
2. 참조가 valid이면 실행, page fault이면 메모리로 paging in
3. free frame을 찾음
4. 새로 allocate된 frame에 원하는 페이지를 read 하도록 secondary storage operation을 scheulde
5. storage read가 완료되면, internal table과 page table에 페이지가 in memory 라고 표시
6. trap에 의해서 interrupt된 명령어 재실행

## Pure Demand Paging
* 요청되기 전까지 페에지를 절대 메모리로 가져오지 않음
* scheme of pure demand paging으로 메모리에 pages가 없는 프로세스를 실행할 수 있다
* OS가 instruction pointer를 page fulat가 있는 process의 first instruction으로 set 할 때, 프로세스의 페이지가 page in 된다.

## Locality of Reference, 참조 지역성(국부성)
* 프로세스가 각 instruction에서 several new page에 즉, instruction을 위한 하나의 페이지와 data를 위한 많은 페이지에 accesses 한다면, multiple page faults가 instruction 마다 일어날 수 있다
* Fortunately, running processes의 분석을 통해 위 동작이 발생할 가능성이 매우 낮다는 것을 알 수 있음
* program은 locality of reference가 있어, demand paging을 이용해서 reasonable performance를 낼 수 있다

## Hardware Support to Demand Paging
* Page Table
    * valid or invalid를 표시하기 위한 기능
* Secondary memory(=swap space)
    * main memory에 없는 페이지를 hold
    * 일반적으로 high-speed disk or NVM device 사용
## Instruction Restart
* page fault 이후 instruction을 다시 시작할 수 있어야 함
* page fault가 발생할 때, 인터럽트된 프로세스의 상테를 저장해야 됨
* 정확하게 같은 위치와 상태에서 프로세스가 재시작 되어야 됨
* instruction fetch 할 때 page fault가 발생하면 다시 fetch 해야 함
* operand를 fetch할 때 page fault가 발생하면 instruction을 다시 fetch 해서 operand를 fetch 해야 함

## Free Frame List
* page fault가 발생했을 때, OS는 secondary storage에서 메인 메모리로 desired memory를 가져와야 함
* page faults를 해결하기 위해서, OS는 free frame list를 유지해야 함

## Copy-on-Wirte
* 프로세스가 shared page에 write 할 때만 shared page를 copy

