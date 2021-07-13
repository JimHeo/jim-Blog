---
title: Python GIL(Global Interpreter Lock)이 뭐에요?
date: 2021-07-06 13:57:09
tags:
    - Python
    - GIL
    - Threading
    - Mutex
categories:
    - Programming
author: Jim
mathjax: true
---

### 들어가며

딥러닝을 입문하며 자연스럽게 파이썬을 함께 공부하게 되었고, 지금은 주 언어로 사용하고 있다. 하지만 나는 파이썬에 대해 얼마나 알고 있을까?

C/C++ 같은 컴파일 언어만 사용했던 나에게 파이썬은 꽤 난해하게 다가왔다. 사실, 파이썬을 사용하면서 느낀 생각은 "파이썬의 철학(The Zen of Python)과 많이 다른데..?" 였다. 파이썬 철학의 핵심 내용은 `명확한 그리고 가급적이면 유일한 하나의 방법이 존재한다(There should be one-- and preferably only one --obvious way to do it)`이며, 이에 따르면 규칙이 굉장히 엄격한 언어라고 한다. 그렇지만 나에게 파이썬의 처음에는 굉장히 자유로운 언어로 보였다.

<!--more-->

#### Life is too short, You need Python!

애초에 멀티 패러다임 언어이기도 하면서, `명시적인 것이 암시적인 것보다 낫다(Explicit is better than implicit)`고 하지만 동적 타입이기 때문에 타입 선언부터 암시적이며 굉장히 유연하다. 마찬가지로, 들여쓰기도 자유도를 제한한다고 하지만 나는 "어차피 들여쓰기는 맞춰서 하는건데 무려 괄호를 안써도 된다고?" 라고 생각했다.. 이렇게 자유도를 주었으면서, 하나의 답으로 수렴하게 되는 언어라니.. 이해가 어려웠다. 그렇지만 그 정도로 자유도가 높은 언어기 때문에 컨벤션을 중시하는 거지 싶기도 했다. 어찌되었든 언어의 자유도가 높을수록 오류가 높아질 확률도 높아질테니 말이다. 파이썬은 실행가능한 의사코드(Executable Pseudocode)라고 불릴 정도로 자연어와 유사한 high-level 언어이며, `파이썬스럽다(Pythonic)`는 말까지 나올정도로 독자적이다. 사람이 쓰기 쉽도록 만든 언어이므로 사람이 읽기 편하게 하는데 가장 중점을 두며, 유연한만큼 문서적으로 제약이 많이 두고 있다고 생각한다. 생산성이 가장 좋다고 불리는 언어로써 - `Life is too short, You need Python.` - 여러 사람이 동일한 컨벤션으로 사용해야 생산성을 극대화할 수 있을 것이다.

역시 파이썬은 아직 어렵다. :joy:

<br>
<center>
<img src="https://imgs.xkcd.com/comics/python.png" width="50%">
<font size="2" color="gray"> xkcd - Python!<font>
</center>
<br>

나는 기본적으로 어떤 언어든 본질은 같기 때문에 머리에 로직만 담고 있으면, 금방 사용할 수 있다는 생각을 하고 있었다. 이 생각이 바뀐 것은 아니지만 최근에는 여기에 +$\alpha$로 언어의 철학을 이해하려는 노력이 필요하다는 생각을 하게 되었다. 클린 코드를 지향한다고 항상 말하지만 클린 코드가 정확히 무엇인지 깊게 고민한 적은 없었던 듯 싶다. 클린 코드를 위한 방법은 여러가지가 있겠으나, 언어의 철학은 언어를 만든 원리와도 같으며, 이를 이해하는 것이 그 첫걸음이라 생각하며 포스트를 작성한다. 파이썬이 왜 Garbage Collection의 방법 중 하나로 Reference Counting을 사용하는지, 왜 GIL 정책을 사용하는지도 파이썬의 기본 원리를 통해 이해할 수 있으리라 믿는다.*(다만, 이번 포스팅에서는 Garbage Collection과 Reference Count에 대해서 깊게 다루지는 않을 듯 하다.)*

### 파이썬에서 멀티 쓰레딩이 사실 싱글 쓰레딩이라구요?

최근 이런 질문을 받았다. (사실상 이 포스트를 쓰게 된 이유. 많은 도움이 되었습니다..:bow:)

> C: 딥러닝 쪽을 주로 공부하셨으니 파이썬 많이 쓰시죠?
> 나: 음.. 네.
> C: 그럼 파이썬에서 멀티 쓰레드를 쓸 때, 내부적으로 싱글 쓰레드로 동작하는 것 알고 계신가요?
> 나: 아.. 들어본 것도 같긴 한데.. 그런..가요..?

많이 부끄러웠다. 당황해서 머리가 새하얘져 이후 멀티 쓰레드와 멀티 프로세스에 대한 대화에서 반대로 말하거나 Numpy의 Vectorization에 대해서도 엉뚱한 말을 했던 것 같다. 이후 이에 대해 찾아보면서 파이썬에서 `GIL(Global Interpreter Lock)`을 사용한다는 것을 알게되었다. 그럼 차근히 프로그램부터 프로세스와 쓰레드의 개념부터 내가 이해하는 언어로 다시 정리해보자.

> **프로그램(Program)**
> `실행 가능한 파일(Executable File in File System)`, 메모리에 할당되지 않은 정적 상태
> 
> **프로세스(Process)**
> `실행 중인 프로그램(Executing Program)`, 프로그램의 인스턴스(Instance of Program), 프로세스가 생성되면서 운영체제로부터 자원을 할당받음, 할당된 범위를 벗어날 수 없음
> 
> **쓰레드(Thread)**
> 프로세스 안에서 실질적으로 `작업을 수행하는 여러 흐름`, 독립적으로 스케쥴링 할 수 있는 `최소 단위(The Smallest Sequence)`, 1개의 프로세스에는 1개 이상의 쓰레드가 존재, 프로세스 내의 쓰레드는 서로 메모리를 공유(Shared Memory)

각 개념은 기본적으로 위와 같으며, 결국 쓰레드는 할당받은 메모리의 범위를 벗어날 수 없는 프로세스의 한계를 보완하기 위해 사용되는 개념이다. 일반적으로 각 프로세스에는 **Code, Data, Heap, Stack**의 형식의 메모리 영역이 할당되며, 쓰레드는 별도의 **Stack**을 다시 할당받게 되고, **Code, Data, Heap**을 **Shared Memory**로 사용하게 된다. 이렇게 쓰레드를 하나의 프로세스 안에서 두 개 이상 사용하면 `동시성(Concurrency)` 프로그래밍을 할 수 있어 더 빠른 속도로 작업을 처리할 수 있게 된다.*(Concurrency와 Parallelism의 차이도 나중에 포스팅을 해보자.)*

그러나, 메모리를 공유한다는 것(프로세스든 쓰레드든)은 양날의 검이다. 정말 개쩌는 코드를 정확하게 작성해서 사용하면 되겠지 싶지만, 혼자 사용할 코드가 아니라면 아무리 날고 기어도 항상 문제가 발생하기 마련이다. 여기서 학부시절 운영체제 강의를 들으며 교수님께서 그렇게 강조하셨던 공유 자원에 접근하는 코드(임계 영역, Critical Section)에 여러 쓰레드가 동시에 진입하게 되는 경쟁 상태(Race Condition)라는 문제가 나온다. Race Condition이 발생하면 데이터가 불일치되는 문제로 이어지기 때문에 **동기화(Synchronization)** 과정을 통해 Race Condition을 방지해야만 한다. 동기화하기 위한 대표적인 조건이 Critical Section에 하나의 쓰레드만 진입할 수 있도록 하는 `상호 배제(Mutual Exclusion, Mutex)`이며, 이에 대한 방법 중 하나가 `Lock`이다.

<br>
<center>
<img src="https://i.pinimg.com/564x/cf/3d/9a/cf3d9a67a7cb5781a15693e24bf92227.jpg" width="50%">
<font size="2" color="gray"> 가망이 없어..<font>
</center>
<br>

파이썬에서도 동일하게 이 논리가 적용된다. 파이썬으로 공유 메모리에 접근할 때도 동기화 과정이 필요하므로 Mutex를 활용하게 된다. 다만, 파이썬에서는 그 활용 방식이 조금 독특할 뿐이다. 모든 객체에 대한 Mutex를 두어 Thread-safe하게 만들려면 너무 복잡하고 쓰레드 간 자원을 요구하는 상태가 엉켜 **교착 상태(Deadlock)**가 발생할 수도 있다. `그래서 파이썬에서는 그냥 다 잠궜단다..` 

<br>
<center>
<img src="https://preview.redd.it/r2zt0z71yfu31.png?width=640&crop=smart&auto=webp&s=446a79718041098ad4031d8981f0e7e093e5b595" width="50%">
<font size="2" color="gray"> 어.....<font>
</center>
<br>

아무튼 그렇게 등장한 것이 바로 GIL이고, 이것이 파이썬 인터프리터에서 한 번에 한 쓰레드에서만 파이썬 Bytecode를 실행하도록 하는 장본인이다. 이로 인해, 아무리 멀티 쓰레딩을 사용한들 실질적으로는 하나의 쓰레드만 모든 자원을 점유하고 다른 쓰레드는 자원이 없으므로 실행할 수 없게 된다. 흔히 "파이썬은 사용은 쉬운데 성능이 좀.."이라고 하는데는 다양한 이유가 있지만 GIL은 아마 그 지분 중 한 축을 당당하게 차지하고 있을 것이다. 그렇지만, GIL을 사용한데도 다 이유가 있을터.. 다시 파이썬이 GIL을 사용하게 된 경위를 들여다보자. ~~([???: 그럼 느그들이 함 해봐.](https://www.artima.com/weblogs/viewpost.jsp?thread=214235))~~<sup>[1](#Removing-GIL)</sup>

### 파이썬은 왜 사서 성능을 낮추었는가

내부적인 동작을 까봤을 때, 사실 파이썬은 단순히 인터프리터 언어로 보기는 어렵다. 파이썬은 `CPython`이라고 하는 C로 되어있는 구현체를 - PyPy나 Jython 등 다른 구현체도 있지만 - 표준 인터프리터로 사용하며 CPython은 인터프리팅 하기 전에 파이썬 코드를 컴파일하는 작업을 거친다. 다만 파이썬의 컴파일 과정에서는 Assembly가 아니라, Assembly와 유사하지만 VM 위에서 실행하도록 하는 `Bytecode`로 번역하게 된다.(이 때 .pyc 파일이 생성된다.) 이 과정은 Java가 Javac와 JVM을 거치는 것과도 비슷하며, Python 자체의 VM위에서 실행되므로 CPU에 독립적인 인터프리터의 특성을 지니게 된다.

파이썬에서 문자열부터 정수나 실수 등 모든 것은 객체로 구성되어있다. 그리고 하나의 객체는 CPython에서 하나의 구조체와 대응된다. 수많은 객체를 통제하는 방법으로 파이썬에서는 객체(CPython의 구조체)에 자신을 참조하는 변수의 수를 저장하는 `Reference Counting`을 두고있다. 또한, 파이썬은 사람이 쉽게 사용할 수 있는 언어로 설계되었기 때문에, `Garbage Collection`<sup>[2](#GC-RC)</sup>을 사용해 메모리 영역의 할당과 해제에 대해 사람이 고민할 필요성을 없애주었다. 파이썬의 Garbage Collection은 Reference Counting을 통해 관리되며, 보통 Reference Counting의 값이 0이 되면 해당 객체의 메모리 할당을 해제하게 된다. 파이썬의 함수 호출 방식이 가뜩이나 `Call by Object-Reference`라고 하는 조금 독특한 방식인데 메모리 할당과 해제를 사람이 직접해야 한다면, 그리고 Reference Counting을 사람이 직접 세어야 한다면, 개판난 코드로 인해 메모리가 줄줄 흐르는 일이 많지 않을까 싶다.*(적어도 나의 경우에는 그럴 것 같다..)*

그런데, 멀티 쓰레딩에서 Reference Counting은 Critical Section으로 여러 쓰레드가 이 영역에 진입하게 되면 Race Condition으로 인해 동기화에 문제가 발생하게 된다. 동기화를 위해 파이썬 코드의 수 많은 객체와 객체들 간 연산에서 일일히 Lock을 걸어주는 일은 GIL을 사용하는 방법보다 훨씬 큰 비용을 초래한다. 또한, 각각 Lock을 갖고 있는다면 개발자의 역량이 얼마나 뛰어난가와 관계없이 Deadlock을 초래할 확률이 높다.

GIL은 **단 하나의 Lock**만을 사용하기 때문에 간단하게 만들 수 있었고, Deadlock의 위험이 없으며, Overhead의 부담이 적었을 것이다. 그래서, 귀도 반 로섬과 다른 파이썬 개발자들은 GIL을 채택하게 되었고 이 방식이 현재까지 이어져 오고 있다.

#### 그럼 그냥 싱글 쓰레드로 써야하나요..?

GIL로 인해 대부분의 멀티 쓰레딩은 싱글 쓰레딩과 성능의 차이가 없으며, 오히려 쓰레드의 `Context-Switching`으로 인해 Overhead가 더 커져 느리게 동작하는 경우도 발생한다.

<br>
<center>
<img src="https://cds.cern.ch/record/2721971/files/Tobecontinued_2_image.jpg?subformat=icon" width="50%">
<font size="2" color="gray"> 업데이트 예정<font>
</center>
<br>

#### Java에서는 GIL 안 쓰던데..?

<br>
<center>
<img src="https://cds.cern.ch/record/2721971/files/Tobecontinued_2_image.jpg?subformat=icon" width="50%">
<font size="2" color="gray"> 업데이트 예정<font>
</center>
<br>

------------------------------------------------------------
<a name="Removing-GIL">[1]</a> 많은 사람들이 GIL을 제거하기 위해 많은 연구를 했지만, 아직 GIL을 제거할 수 있는 방법은 없는 듯 하다. GIL을 제거하기 위해서는 싱글 쓰레드의 성능을 떨어뜨리지 않아야한다는 전제가 있는데 이것이 쉽지 않다고 한다.
<a name="GC-RC">[2]</a> Reference Counting에 대해 순환 참조(Reference Cycle)가 발생할 경우에는 Cyclic Garbage Collector를 별도로 사용한다고 한다.
