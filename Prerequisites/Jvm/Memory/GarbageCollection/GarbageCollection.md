# Garbage Collection 이란
Garbage Collection은 메모리 관리 기법 중의 하나로, 프로그램이 동적으로 할당했던 메모리 영역 중에서 필요없게 된 영역을 해제하는 기능이다.

# Garbage Collection 과정
HotSpot VM에서는 크게 2개로 물리적 공간을 나누었다. 둘로 나눈 공간이 **Young 영역**과 **Old 영역**이다.

- **Young 영역**: 새롭게 생성한 객체의 대부분이 여기에 위치한다. 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 매우 많은 객체가 Young 영역에 생성되었다가 사라진다. 이 영역에서 객체가 사라질때 Minor GC가 발생한다고 말한다.

- **Old 영역**: 접근 불가능 상태로 되지 않아 Young 영역에서 살아남은 객체가 여기로 복사된다. 이 영역에서 객체가 사라질 때 Major GC(혹은 Full GC)가 발생한다고 말한다.

## **Young 영역의 구성**

**Young 영역은 3개의 영역으로 나뉜다. (Eden 영역, 2개의 Survivor 영역)**

- 새로 생성한 대부분의 객체는 Eden 영역에 위치한다.
- Eden 영역에서 GC가 한 번 발생한 후 살아남은 객체는 Survivor 영역 중 하나로 이동된다.
- Eden 영역에서 GC가 발생하면 이미 살아남은 객체가 존재하는 Survivor 영역으로 객체가 계속 쌓인다.
- 하나의 Survivor 영역이 가득 차게 되면 그 중에서 살아남은 객체를 다른 Survivor 영역으로 이동한다. 그리고 가득 찬 Survivor 영역은 아무 데이터도 없는 상태로 된다.
- 이 과정을 반복하다가 계속해서 살아남아 있는 객체는 Old 영역으로 이동하게 된다.

<p align="center"><img src="https://d2.naver.com/content/images/2015/06/helloworld-1329-3.png"></p>

## **Old 영역에 대한 GC**

* **Serial GC**
  * Old 영역의 GC는 mark-sweep-compact 이라는 알고리즘을 사용
    1. Old 영역에 살아 있는 객체를 식별(Mark) 
    2. 그 다음에는 힙(heap)의 앞 부분부터 확인하여 살아 있는 것만 남긴다(Sweep)
    3. 각 객체들이 연속되게 쌓이도록 힙의 가장 앞 부분부터 채워서 객체가 존재하는 부분과 객체가 없는 부분으로 나눈다(Compaction)
 
* **Parallel GC**
  * Parallel GC는 Serial GC와 기본적인 알고리즘은 같다. 그러나 Serial GC는 GC를 처리하는 스레드가 하나인 것에 비해, Parallel GC는 GC를 처리하는 쓰레드가 여러 개이다. 
  
* **Parallel Old GC(Parallel Compacting GC)**
  * Parallel GC와 비교하여 Old 영역의 GC 알고리즘만 다르다. 이 방식은 Mark-Summary-Compaction 단계를 거친다.
  
* **Concurrent Mark & Sweep GC(이하 CMS)**
  * 초기 Initial Mark 단계에서는 클래스 로더에서 가장 가까운 객체 중 살아 있는 객체만 찾는 것으로 끝내고 Concurrent Mark 단계에서는 방금 살아있다고 확인한 객체에서 참조하고 있는 객체들을 따라가면서 확인한다. 이 단계의 특징은 다른 스레드가 실행 중인 상태에서 동시에 진행된다는 것이다.
  
  <p align="center"><img src="https://d2.naver.com/content/images/2015/06/helloworld-1329-5.png"></p>
  
* **G1(Garbage First) GC**

  * G1 GC는 바둑판의 각 영역에 객체를 할당하고 GC를 실행한다. 그러다가, 해당 영역이 꽉 차면 다른 영역에서 객체를 할당하고 GC를 실행한다. 즉, 지금까지 설명한 Young의 세가지 영역에서 데이터가 Old 영역으로 이동하는 단계가 사라진 GC 방식

자세한 내용 참조: https://d2.naver.com/helloworld/1329

# stop-the-world 란
stop-the-world란, GC을 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것이다. stop-the-world가 발생하면 GC를 실행하는 쓰레드를 제외한 나머지 쓰레드는 모두 작업을 멈춘다. GC 작업을 완료한 이후에야 중단했던 작업을 다시 시작한다. 어떤 GC 알고리즘을 사용하더라도 stop-the-world는 발생한다.
GC 튜닝이란 이 stop-the-world 시간을 줄이는 것이다.

# Java Reference와 GC

Java GC는 객체가 가비지인지 판별하기 위해서 reachability라는 개념을 사용한다. 어떤 객체에 유효한 참조가 있으면 
**reachable**로, 없으면 **unreachable**로 구별하고, unreachable 객체를 가비지로 간주해 GC를 수행한다.

힙에 있는 객체들에 대한 참조는 다음 4가지 종류 중 하나이다.

- 힙 내의 다른 객체에 의한 참조
- Java 스택, 즉 Java 메서드 실행 시에 사용하는 지역 변수와 파라미터들에 의한 참조
- 네이티브 스택, 즉 JNI(Java Native Interface)에 의해 생성된 객체에 대한 참조
- 메서드 영역의 정적 변수에 의한 참조

이들 중 힙 내의 다른 객체에 의한 참조를 제외한 나머지 3개가 root set으로, reachability를 판가름하는 기준이 된다.

<p align="center"><img src="https://d2.naver.com/content/images/2015/06/helloworld-329631-2.png"></p>

root set으로부터 시작한 참조 사슬에 속한 객체들은 reachable 객체이고, 이 참조 사슬과 무관한 객체들이 unreachable 객체로 GC 대상이다. 

## Weak Reference

<p align="center"><img src="https://d2.naver.com/content/images/2015/06/helloworld-329631-5.png"></p>

**녹색 객체는 WeakReference 로만 참조된 weakly reachable 객체이고 파란색 객체는 strongly reachable 객체이다.**

GC가 동작할 때, unreachable 객체뿐만 아니라 weakly reachable 객체도 가비지 객체로 간주되어 메모리에서 회수된다.

자세한 내용 참조: https://d2.naver.com/helloworld/329631
