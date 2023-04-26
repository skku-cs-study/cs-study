# CPU Scheduling

컴퓨터안에는 여러 종류의 job(=process)이 섞여 있기때문에 CPU스케줄링이 필요함. 즉, CPU를 효율적으로 사용하기 위해선 어떤 프로세스에게 CPU를 줄지 정해야하고 이를 위해서 CPU스케줄링이 필요한 것.

### 비선점(nonpreemptive)

: CPU를 뺏지 않고 자진해서 반납하는 것.(I/O요청하는 시스템콜, 프로세스 종료)

### 선점(preemptive)

: 강제로 CPU를 뺏음.(timer interrupt 등)



# 스케줄링 알고리즘


### Scheduling Criteria(성능 척도) 또는 Perfomance Index

- 시스템 입장에서의 성능 척도
→ CPU하나가지고 최대한 일을 많이 시키면 좋은 것.
    - CPU utilization(이용률): 전체 시간 중에서 CPU가 놀지 않고 일한 시간.
    - Throughput(처리량): 주어진 시간동안 몇 개의 작업을 처리했는지.

- 프로그램 입장에서의 성능 척도
→ CPU를 빨리 얻어서 빨리 끝나면 좋은 것.
    - Turnaround time(소요시간, 반환시간):
    CPU를 쓰러 들어와서 다 쓰고 나갈때까지 걸린 시간(기다린 시간, CPU쓴 시간 등 다 합친 것).
    완료될때까지 걸린 시간이라고  생각하면됨.
        
    - Waiting time(대기시간):
    순수하게 기다린 시간
    - Response time(응답시간): 
    ready queue에 들어와서 처음으로 CPU를 얻기까지 걸린 시간
    

## FCFS(First Come First Served)

- 먼저 들어온 것을 먼저 처리
- nonpreemptive이므로 한 번 CPU를 얻으면 내놓지 않음.
    - **Convoy Effect 발생할 수 있음.**
    → 오래걸리는 프로세스가 먼저 도착해서, 뒤의 다른 프로세스들이 지나치게 오래 기다리는 현상
    - 들어오는 순서에 따라 시간이 엄청나게 차이남.

## SJF(Shortest Job First)

- CPU Burst가 제일 짧은 프로세스에게 CPU를 먼저 주는 것.
→ **전체적인 줄이 짧아져서 average waiting time은 가장 짧음(Preemptive인 경우)**
- 두 가지 방식 가능
    - Nonpreemptive인 경우
    → 일단 짧은 애가 CPU를 쓰고 있으면, 더 짧은 애가 도착하더라도 CPU를 뺏기지않음.
    - Preemptive인 경우
    → 더 짧은 프로세스가 도착하면 CPU를 뺏어서 넘겨줌**(SRTF라고도함. Shortest Remaining)**
    
    ⇒ **nonpreemptive는 작업이 끝나면 스케줄링을 하는데, preemptive는 새로운 작업이 들어왔을때 스케줄링함.**
    

**문제점**

- Starvation
→ CPU사용시간이 긴 프로세스들은 영원히 못 받을 수도 있음.
- CPU사용시간을 미리 알 수 없기때문에, 다음 CPU burst를 지수평균을 통해 예측.

## Priority Scheduling

- 우선순위가 가장 높은 프로세스에게 CPU를 줌.
- 두 가지 방식 가능
    - Nonpreemptive
    → 우선순위가 더 높은 프로세스가 도착해도 CPU를 내어주지않음(CPU사용시간 보장)
    - Preemptive
    → 우선순위가 더 높은 프로세스가 도착하면 CPU를 넘겨줌.
- 우선순위 값이 작을수록 우선순위가 높음.
- SJF도 CPU 사용시간을 우선순위로 사용하는 일종의 우선순위 큐

**문제점**

- Starvation
→ 우선순위가 낮은 애들은 영원히 못받을 수도 있음.
**⇒ 기다릴수록 우선순위를 점점 높여주는 Aging 기법을 이용해 해결.**

## Round Robin(RR)

- 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐.
→ **할당시간이 끝나면 CPU는 다른 프로세스로 넘어가므로 Preemptive**
- **응답시간이 빠름**
→ **n개의 프로세스가 ready queue에 있고 할당시간이 q인 경우, 어떤 프로세스도 (n - 1)q 이상 기다리지않음**
- 할당시간을 매우 크게 잡으면 FCFS와 동일
할당시간을 매우 작게 잡으면 context switch 오버헤드가 커짐

## Multilevel Queue

- 작업들을 여러 종류의 그룹으로 나누어 여러 개의 큐를 이용하는 방법
→ 각 큐 마다 우선순위가 있음.
- ready queue를 분할하여 우선순위가 높은 interactive한 job들은 foreground에, 낮은 애들은 background
    - foreground는 interactive하므로 응답시간이 빠른 RR이 나음
    (인터넷창을 보여주는 등)
    - background는 응답시간이 빠를 필요없으므로 최대한 context switch 오버헤드를 줄이기 위해 FCFS가 적절
- 큐에 대한 스케줄링이 필요함.
    - 우선순위 고정
    → 무조건 우선순위가 높은 애한테 우선으로 주는 방식.
    → Starvation 발생 가능
    - Time slice
    → 각 큐에 CPU time을 적절한 비율로 할당(예를 들어, 가장 높은 큐에 80정도 주고 나머지를 나누는 등)

## Multilevel Feedback Queue

- 다단계 큐에서 자신의 time quantum을 다 채운 프로세스는 우선순위를 낮춘 큐에 들어가고, 다 못채운 프로세스는 원래의 우선순위 큐에 들어감.
- 짧은 작업들은 도착하자마자 CPU를 얻어서 바로 빠져나갈 수 있으므로 유리하고, 긴 작업들은 점점 우선순위가 낮아질거임.
- Interrupt가 잦은 I/O작업들에 우선권을 줌.
- 우선순위가 높은 큐에서는 time quantum을 짧게 할당하고, 우선순위가 낮은 큐로 내려갈수록 time quantum을 길게줌. 
가장 우선순위가 낮은 큐는 FCFS
- 예측이 필요없이 처음에는 무조건 우선순위가 가장 높은 큐
- 처리 시간이 짧은 프로세스를 먼저 처리하기때문에 Average Turnaround time을 줄여줌.



# Multiple-Processor Scheduling


CPU가 여러 개인 것을 멀티 프로세서라고 함. 그리고 멀티 프로세서의 경우 스케줄링은 더욱 복잡해짐

**Load balancing과 Affinity를 고려해야함**

두 가지 방식이 가능

- **Asymmetric multiprocessing**

→ 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고, 나머지는 거기에 따름.
- **Symmetric multiprocessing(SMP)**

→ 각 프로세서가 각자 알아서 스케줄링 결정. 대부분의 멀티프로세서 스케줄링에 사용

## Single-Queue Scheduling

- 싱글 프로세서 시스템에서 쓰던 스케줄러를 그대로 사용하는 것으로, 하나의 큐에 작업들을 넣어서 처리함.
- 단순

**문제점**

- CPU에서 공유 데이터에 접근할 때, Lock 등을 이용하여 정확성을 보장해줘야함. 하지만 Lock을 사용하면 **성능을 저하**시킴. 
즉, 단순하지만 실제 프로그램 수행 시간보다 lock을 처리하는 작업에 더 많은 시간을 사용할 수도 있음.
- 멀티 프로세서에서의 스케줄러는 캐시 선호도를 고려해서 프로그램을 동일한 CPU에서 실행되도록 해줘야함. 만약, A라는 프로그램이 첫 번째 CPU에서 할당된 시간만큼 작업한 이후에 다른 CPU에서 작업할 수도 있음.
    
    근데 SQMS에서는 모든 프로세스에 대해 이것이 지켜지지 않을 수도 있음.
    
    **Queue: A → B → C → D → E**  
    
    **CPU 0**: A → E → D → C → B
    
    **CPU 1**:  B → A → E → D → C
    
    **CPU 2**: C → B → A → E → D 
    
    **CPU 3**: D → C → B → A → E
    
    ⇒ 위처럼 수행되는 경우 A가 CPU0에서 수행된 이후엔 다른 CPU에서 수행되므로, 별도의 메커니즘을 이용해 다음과 같이 바꿀 수 있음.
    
    **CPU 0**: A → E → A → A → A
    
    **CPU 1**:  B → B → E → B → B
    
    **CPU 2**: C → C → C → E → C 
    
    **CPU 3**: D → D → D → D → E
    
    ⇒ A,B,C,D는 문제가 해결됐지만, E의 경우는 그대로임.
    

## Multi-Queue Scheduling

- 큐를 여러 개 사용하여 SQMS의 문제점을 보완한 스케줄링 기법
- CPU는 각각 하나 또는 그 이상의 큐를 갖고 있고, 각각의 큐는 RR과 같은 특정한 스케줄링 방법을 사용.

    **Queue0: A → C**
    
    **Queue1: B → D**
    
    **CPU0**: A → A → C → C → A → A → C → C → …
    
    **CPU1**: B → B → D → D → B → B → D → D → …
    
    ⇒ CPU는 자신이 처리할 큐에 있는 작업들만 처리하므로 문제가 없어보임.
    

**문제점**

- Queue0에 있는 C작업이 끝난 경우나 A,C 작업이 모두 끝난 경우를 생각해보면, CPU1만 일하게됨.

→ migration을 통해 작업을 옮기면 됨.
- 하지만 작업이 각각 1개, 2개 있는 경우를 생각해보면, load balance를 맞추기 위해 계속해서 프로세스들이 이동할 것임.

→ 오버헤드가 커질 것이고, 만약 pull migration을 하지 않는다면 load balance에 문제가 생김

## Linux Multiprocessor Scheduler

- 실제 리눅스에서는 O(1)스케줄러, CFS, BFS라는 스케줄러 사용
- O(1)과 CFS는 모두 여러 개의 큐를 사용하지만, BFS는 하나의 큐만 사용. 

→ SQMS, MQMS 모두 사용 가능
- O(1)은 우선순위 기반 스케줄러로서, 프로세스의 우선순위를 시간에 따라 변경하여 우선순위가 가장 높은 작업을 선택함. 상호작용을 중요하게 생각
- CFS는 여러 개의 큐를 사용한 비례배분 방식
- BFS는 단일 큐 사용한 비례배분 방식이며, Earliest Eligible Virtual Deadline First(EEVDF)라는 방식에 기반을 둠.
    
    [참고](https://icksw.tistory.com/125)
  
  

# Thread Scheduling


- 쓰레드 중 kernel level thread만 스케줄되고, User level thread는 스레드 라이브러리에 의해 어떤 스레드를 스케줄할지 결정함.

→ 사용자 수준 스레드는 운영체제가 모르기때문에, 운영체제 입장에서는 프로세스에게 CPU를 줄지 안줄지 결정만하고, 해당 프로세스에게 CPU가 갔을때 CPU내부에서 어떤 스레드에게 줄지는 프로세스 내부에서 결정함. 즉, 사용자 프로세스가 직접 결정.
- kernel level thread는 커널의 단기 스케줄러가 어떤 스레드를 스케줄할지 결정
