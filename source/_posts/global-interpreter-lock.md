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

## 들어가며

딥러닝을 입문하며 자연스럽게 파이썬을 함께 공부하게 되었고, 지금은 주 언어로 사용하고 있다. 하지만 나는 파이썬에 대해 얼마나 알고 있을까?

C/C++ 같은 컴파일 언어만 사용했던 나에게 파이썬은 꽤 난해하게 다가왔다. 사실, 파이썬을 사용하면서 느낀 생각은 "파이썬의 철학(The Zen of Python)과 많이 다른데..?" 였다.

파이썬 철학의 핵심 내용은 `명확한 그리고 가급적이면 유일한 하나의 방법이 존재한다(There should be one-- and preferably only one --obvious way to do it)`이며, 이에 따르면 규칙이 굉장히 엄격한 언어라고 한다. 그렇지만 나에게 파이썬의 처음에는 굉장히 자유로운 언어로 보였다.

<!--more-->

</br>

### Life is too short, You need Python!

애초에 멀티 패러다임 언어이기도 하면서, `명시적인 것이 암시적인 것보다 낫다(Explicit is better than implicit)`고 하지만 동적 타입이기 때문에 타입 선언부터 암시적이며 굉장히 유연하다. 마찬가지로, 들여쓰기도 자유도를 제한한다고 하지만 나는 "어차피 들여쓰기는 맞춰서 하는건데 무려 괄호를 안써도 된다고?" 라고 생각했다..

이렇게 자유도를 주었으면서, 하나의 답으로 수렴하게 되는 언어라니.. 이해가 어려웠다. 그렇지만 그 정도로 자유도가 높은 언어기 때문에 컨벤션을 중시하는 거지 싶기도 했다. 어찌되었든 언어의 자유도가 높을수록 오류가 높아질 확률도 높아질테니 말이다.

파이썬은 실행가능한 의사코드(Executable Pseudocode)라고 불릴 정도로 자연어와 유사한 high-level 언어이며, `파이썬스럽다(Pythonic)`는 말까지 나올정도로 독자적이다. 사람이 쓰기 쉽도록 만든 언어이므로 사람이 읽기 편하게 하는데 가장 중점을 두며, 유연한만큼 문서적으로 제약이 많이 두고 있다고 생각한다.

생산성이 가장 좋다고 불리는 언어로써 - `Life is too short, You need Python.` - 여러 사람이 동일한 컨벤션으로 사용해야 생산성을 극대화할 수 있을 것이다.

역시 파이썬은 아직 어렵다. :joy:

<br>
<center>
<img src="https://imgs.xkcd.com/comics/python.png" width="50%">
<font size="2" color="gray"> xkcd - Python!<font>
</center>
<br>

나는 기본적으로 어떤 언어든 본질은 같기 때문에 머리에 로직만 담고 있으면, 금방 사용할 수 있다는 생각을 하고 있었다. 이 생각이 바뀐 것은 아니지만 최근에는 여기에 +$\alpha$로 언어의 철학을 이해하려는 노력이 필요하다는 생각을 하게 되었다.

클린 코드를 지향한다고 항상 말하지만 클린 코드가 정확히 무엇인지 깊게 고민한 적은 없었던 듯 싶다. 클린 코드를 위한 방법은 여러가지가 있겠으나, 언어의 철학은 언어를 만든 원리와도 같으며, 이를 이해하는 것이 그 첫걸음이라 생각하며 포스트를 작성한다.

파이썬이 왜 Garbage Collection의 방법 중 하나로 Reference Counting을 사용하는지, 왜 GIL 정책을 사용하는지도 파이썬의 기본 원리를 통해 이해할 수 있으리라 믿는다.*(다만, 이번 포스팅에서는 Garbage Collection과 Reference Count에 대해서 깊게 다루지는 않을 듯 하다.)*

</br>

## 파이썬에서 멀티 쓰레딩이 사실 싱글 쓰레딩이라구요?

최근 이런 질문을 받았다. (사실상 이 포스트를 쓰게 된 이유. 많은 도움이 되었습니다..:bow:)

> C: 딥러닝 쪽을 주로 공부하셨으니 파이썬 많이 쓰시죠?
> 나: 음.. 네.
> C: 그럼 파이썬에서 멀티 쓰레드를 쓸 때, 내부적으로 싱글 쓰레드로 동작하는 것 알고 계신가요?
> 나: 아.. 들어본 것도 같긴 한데.. 그런..가요..?

많이 부끄러웠다. 당황해서 머리가 새하얘져 이후 멀티 쓰레드와 멀티 프로세스에 대한 대화에서 반대로 말하거나 Numpy의 Vectorization에 대해서도 엉뚱한 말을 했던 것 같다.

이후 이에 대해 찾아보면서 파이썬에서 `GIL(Global Interpreter Lock)`을 사용한다는 것을 알게되었다.

그럼 차근히 프로그램부터 프로세스와 쓰레드의 개념부터 내가 이해하는 언어로 다시 정리해보자.

> **프로그램(Program)**
> `실행 가능한 파일(Executable File in File System)`, 메모리에 할당되지 않은 정적 상태
> 
> **프로세스(Process)**
> `실행 중인 프로그램(Executing Program)`, 프로그램의 인스턴스(Instance of Program), 프로세스가 생성되면서 운영체제로부터 자원을 할당받음, 할당된 범위를 벗어날 수 없음
> 
> **쓰레드(Thread)**
> 프로세스 안에서 실질적으로 `작업을 수행하는 여러 흐름`, 독립적으로 스케쥴링 할 수 있는 `최소 단위(The Smallest Sequence)`, 1개의 프로세스에는 1개 이상의 쓰레드가 존재, 프로세스 내의 쓰레드는 서로 메모리를 공유(Shared Memory)

각 개념은 기본적으로 위와 같으며, 결국 쓰레드는 할당받은 메모리의 범위를 벗어날 수 없는 프로세스의 한계를 보완하기 위해 사용되는 개념이다. 일반적으로 각 프로세스에는 **Code, Data, Heap, Stack**의 형식의 메모리 영역이 할당되며, 쓰레드는 별도의 **Stack**을 다시 할당받게 되고, **Code, Data, Heap**을 **Shared Memory**로 사용하게 된다.

이렇게 쓰레드를 하나의 프로세스 안에서 두 개 이상 사용하면 `동시성(Concurrency)` 프로그래밍을 할 수 있어 더 빠른 속도로 작업을 처리할 수 있게 된다.*(Concurrency와 Parallelism의 차이도 나중에 포스팅을 해보자.)*

그러나, 메모리를 공유한다는 것(프로세스든 쓰레드든)은 양날의 검이다. 정말 개쩌는 코드를 정확하게 작성해서 사용하면 되겠지 싶지만, 혼자 사용할 코드가 아니라면 아무리 날고 기어도 항상 문제가 발생하기 마련이다.

여기서 학부시절 운영체제 강의를 들으며 교수님께서 그렇게 강조하셨던 공유 자원에 접근하는 코드(임계 영역, Critical Section)에 여러 쓰레드가 동시에 진입하게 되는 경쟁 상태(Race Condition)라는 문제가 나온다.

Race Condition이 발생하면 데이터가 불일치되는 문제로 이어지기 때문에 **동기화(Synchronization)** 과정을 통해 Race Condition을 방지해야만 한다.

동기화하기 위한 대표적인 조건이 Critical Section에 하나의 쓰레드만 진입할 수 있도록 하는 `상호 배제(Mutual Exclusion, Mutex)`이며, 이에 대한 방법 중 하나가 `Lock`이다.

<br>
<center>
<img src="https://i.pinimg.com/564x/cf/3d/9a/cf3d9a67a7cb5781a15693e24bf92227.jpg" width="50%">
<font size="2" color="gray"> 가망이 없어..<font>
</center>
<br>

파이썬에서도 동일하게 이 논리가 적용된다. 파이썬으로 공유 메모리에 접근할 때도 동기화 과정이 필요하므로 Mutex를 활용하게 된다. 다만, 파이썬에서는 그 활용 방식이 조금 독특할 뿐이다.

모든 객체에 대한 Mutex를 두어 Thread-safe하게 만들려면 너무 복잡하고 쓰레드 간 자원을 요구하는 상태가 엉켜 **교착 상태(Deadlock)**가 발생할 수도 있다. `그래서 파이썬에서는 그냥 다 잠궜단다..` 

<br>
<center>
<img src="https://preview.redd.it/r2zt0z71yfu31.png?width=640&crop=smart&auto=webp&s=446a79718041098ad4031d8981f0e7e093e5b595" width="50%">
<font size="2" color="gray"> 어.....<font>
</center>
<br>

아무튼 그렇게 등장한 것이 바로 GIL이고, 이것이 파이썬 인터프리터에서 한 번에 한 쓰레드에서만 파이썬 Bytecode를 실행하도록 하는 장본인이다. 이로 인해, 아무리 멀티 쓰레딩을 사용한들 실질적으로는 하나의 쓰레드만 모든 자원을 점유하고 다른 쓰레드는 자원이 없으므로 실행할 수 없게 된다.

흔히 "파이썬은 사용은 쉬운데 성능이 좀.."이라고 하는데는 [다양한 이유](http://jakevdp.github.io/blog/2014/05/09/why-python-is-slow/)가 있겠으나 GIL은 아마 그 지분 중 한 축을 당당하게 차지한다고 생각한다. 그렇지만, GIL을 사용한데도 다 이유가 있을터.. 다시 파이썬이 GIL을 사용하게 된 경위를 들여다보자. ~~([???: 그럼 느그들이 함 해봐.](https://www.artima.com/weblogs/viewpost.jsp?thread=214235))~~<sup>[1](#Removing-GIL)</sup>

</br>

## 파이썬은 왜 사서 성능을 낮추었는가

내부적인 동작을 까봤을 때, 사실 파이썬은 단순히 인터프리터 언어로 보기는 어렵다.

파이썬은 `CPython`이라고 하는 C로 되어있는 구현체를 - PyPy나 Jython 등 다른 구현체도 있지만 - 표준 인터프리터로 사용하며 CPython은 인터프리팅 하기 전에 파이썬 코드를 컴파일하는 작업을 거친다. 다만 파이썬의 컴파일 과정에서는 Assembly가 아니라, Assembly와 유사하지만 VM 위에서 실행하도록 하는 `Bytecode`로 번역하게 된다.(이 때 .pyc 파일이 생성된다.)

이 과정은 자바가 Javac와 JVM을 거치는 것과도 비슷하며, Python 자체의 VM위에서 실행되므로 CPU에 독립적인 인터프리터의 특성을 지니게 된다. 파이썬에서 문자열부터 정수나 실수 등 모든 것은 객체로 구성되어있다. 그리고 하나의 객체는 CPython에서 하나의 구조체와 대응된다.

수많은 객체를 통제하는 방법으로 파이썬에서는 객체(CPython의 구조체)에 자신을 참조하는 변수의 수를 저장하는 `Reference Counting`을 두고있다. 또한, 파이썬은 사람이 쉽게 사용할 수 있는 언어로 설계되었기 때문에, `Garbage Collection`<sup>[2](#GC-RC)</sup>을 사용해 메모리 영역의 할당과 해제에 대해 사람이 고민할 필요성을 없애주었다.

파이썬의 Garbage Collection은 Reference Counting을 통해 관리되며, 보통 Reference Counting의 값이 0이 되면 해당 객체의 메모리 할당을 해제하게 된다.

파이썬의 함수 호출 방식이 가뜩이나 `Call by Object-Reference`라고 하는 조금 독특한 방식인데 메모리 할당과 해제를 사람이 직접해야 한다면, 그리고 Reference Counting을 사람이 직접 세어야 한다면, 개판난 코드로 인해 메모리가 줄줄 흐르는 일이 많지 않을까 싶다.*(적어도 나의 경우에는 그럴 것 같다..)*

그런데, 멀티 쓰레딩에서 Reference Counting은 Critical Section으로 여러 쓰레드가 이 영역에 진입하게 되면 Race Condition으로 인해 동기화에 문제가 발생하게 된다.

동기화를 위해 파이썬 코드의 수 많은 객체와 객체들 간 연산에서 일일히 Lock을 걸어주는 일은 GIL을 사용하는 방법보다 훨씬 큰 비용을 초래한다.

또한, 각각 Lock을 갖고 있는다면 개발자의 역량이 얼마나 뛰어난가와 관계없이 Deadlock을 초래할 확률이 높다.

GIL은 **단 하나의 Lock**만을 사용하기 때문에 간단하게 만들 수 있었고, Deadlock의 위험이 없으며, Overhead의 부담이 적었을 것이다. 그래서, 귀도 반 로섬과 다른 파이썬 개발자들은 GIL을 채택하게 되었고 이 방식이 현재까지 이어져 오고 있다.

<br>
<center>
<img src="https://i.pinimg.com/originals/70/10/5f/70105ffd8406c9766503d3f15b6c82b1.jpg" width="35%">
<font size="2" color="gray"> 아하! 고마워요 GIL!<font>
</center>
<br>

</br>

### 그럼 그냥 싱글 쓰레드로 써야하나요..?

우선, Real Python에 포스팅된 [What Is the Python Global Interpreter Lock (GIL)?](https://realpython.com/python-gil/)에서 싱글 쓰레드와 멀티 쓰레드의 성능을 비교하기 위한 코드 일부를 발췌해서 실행해보았다.

> Single-threading 성능

```python
import time

COUNT = 50000000

def countdown(n):
    while n > 0:
        n -= 1

start = time.time()
countdown(COUNT)
end = time.time()

print('Time taken in seconds -', end - start)

# Single Threading
# Time taken in seconds - 2.1882541179656982
```

</br>

> Multi-threading 성능

```python
import time
from threading import Thread

COUNT = 50000000

def countdown(n):
    while n > 0:
        n -= 1

t1 = Thread(target=countdown, args=(COUNT // 2, ))
t2 = Thread(target=countdown, args=(COUNT // 2, ))

start = time.time()
t1.start()
t2.start()
t1.join()
t2.join()
end = time.time()

print('Time taken in seconds -', end - start)

# Multi Threading
# Time taken in seconds - 2.1965391635894775
```

</br>

결과에서 볼 수 있듯이 GIL로 인해 대부분의 멀티 쓰레딩은 싱글 쓰레딩과 성능의 차이가 없으며, 오히려 쓰레드의 `Context-Switching`으로 인해 Overhead가 더 커져 느리게 동작하는 경우도 발생한다.

그렇기 때문에 파이썬에서 멀티 쓰레딩은 잘 사용하지 않으며 동시성 프로그래밍을 해야 할 경우에는 가급적 멀티 프로세싱으로 대체해서 사용한다.

**그러니까 사실은 가급적이면 그냥 싱글 쓰레드로 사용하라는 말이다.**

그렇다면, 왜 파이썬 개발자들은 싱글 쓰레드만도 못한 성능의 이런 병목(Bottleneck)을 굳이 만들었을까? 그냥 멀티 쓰레드 구현 자체를 안해도 되지 않았을까?

그나마 다행히도 CPU-bound가 아닌 Sleep이나 I/O-bound(입출력에 대한 작업, File, Database, Network)에서는 위 내용이 적용되지 않는다.

GIL을 채택한 이유로 위에서 말한 것 이외에도 시기적인 측면에서, Python 자체가 개발될 당시에는 싱글코어의 사용이 일반적이었기 때문에 멀티코어 성능을 향상시키기 위한 목적보다는 다중 I/O 이벤트를 처리하기 위해 GIL을 채택한 것으로 보인다.

그러나 I/O-bound에서는 보통 I/O 이벤트에 대해 처리하는 비용보다 이벤트가 발생하기까지 기다리는 비용이 더 크기 때문에 멀티 쓰레딩을 사용하기보다는 비동기 프로그래밍을 사용하는게 훨씬 이득이다.

**그러니까 다시 말하지만 가급적이면 그냥 싱글 쓰레드로 사용하라는 말이다.**

</br>

### Java에서는 GIL 안 쓰던데..?

한 가지 궁금한 점이 생겼다.

겉으로 보기에는 자바도 파이썬과 비슷하게 Garbage Collection을 사용하며, 컴파일로 자바 Bytecode로 번역하고(자바는 .class 파일을 생성한다.) JVM 위에서 인터프리터로 Bytecode를 실행한다.

그런데 자바에서는 GIL을 사용하지 않는다. 자바(1995년)가 파이썬(1991년)에 비해 늦게 릴리즈되긴 했지만 그래도 4년밖에 차이가 나지 않는다. 그렇기 때문에 시기적으로 접근하기에는 무리가 있다. 자바가 GIL이 없는 이유에 대한 레퍼런스가 많지는 않으나 감사하게도 먼저 이러한 고민을 하신 분의 글을 찾게 되어 해당 [포스트](https://velog.io/@litien/GIL-Java%EC%97%90%EB%8A%94-%EC%97%86%EB%8D%98%EB%8D%B0)를 인용하고자 한다. :pray:

파이썬에서 `Reference Counting`을 사용하는 것과 달리 자바에서는 기본적으로 Garbage Collection을 `Mark and Sweep`이라는 방식으로 관리한다.(현재는 개선된 방식을 사용한다.)

해당 방식은 메모리가 일정 이상 찼을 때 실행되며, 객체를 구성할 때 Flag로 1bit를 추가로 두어 Root 객체(Stack이나 Data에 저장된 현재 Scope의 지역 변수와 전역 변수)부터 접근 가능한 객체들의 Flag 모두 마킹하는 `Mark` 단계와 메모리 공간을 순회하며 마킹되지 않은 객체의 메모리를 해제하는 `Sweep` 단계가 각각 진행된다.

즉, Reference Counting 방식이 객체의 참조가 변경될 때마다 Critical Section에 진입을 막기 위해 Atomic한 연산을 수행해야 하는 것과 달리 Mark and Sweep에서는 이를 고려할 필요가 없다. 그렇기 때문에 자연스럽게 자바에서는 GIL을 사용할 필요가 없어진다.

대신, Mark 단계에서 Flag에 마킹을 하기 위해 모든 쓰레드를 일시정지시키는 `Stop-the-world`(여기서 발생하는 비용을 줄이는 것도 하나의 챌린지이다.)를 통해 Garbage Collection의 Atomic을 보장한다.

Mark and Sweep 방식은 인터프리터에 Lock을 두고 있지 않아 여러 쓰레드가 인터프리터에 접근할 수 있다.

다만, Reference Counting 방식이 바로 메모리를 회수하는 반면 Mark and Sweep은 메모리를 쌓고 있기 때문에 공간적인 측면의 비용을 감수해야한다. 또한, 객체 소멸 시점을 예측하기 어렵다는 단점도 존재한다.

앞서 말했듯 파이썬은 CPython를 디폴트로 하고 있으며, 이는 곧 `malloc()`과 `free()`에 대한 연산을 기본적으로 사용한다는 뜻이기도 하다. `malloc()`과 `free()`은 자바의 방식처럼 메모리를 적재해놓고 있으면 메모리 누수의 위험이 발생한다. 그렇기에 파이썬은 Reference Counting을 두어 바로바로 메모리를 해제해주는 방식을 사용하게 된 것으로 유추할 수 있다.

</br>

## 마치며

쓰다보니 말이 길어졌다.

그래도 포스팅을 하며 스스로 내용을 정리할 수 있었으니 되었다.

파이썬을 애초에 싱글 쓰레드 이상으로 사용한 적이 거의 없었거니와 Tensorflow와 같은 프레임워크에서 코어 연산은 모두 C++로 동작하다보니 이런 고민을 잘 해본적이 없던 것 같다. (Tensorflow에서는 자체적으로 GIL을 해제해준다고도 한다.)

언어가 만들어진 여러 배경과 언어의 철학을 이해하는 노력을 더 기르면 자연스럽게 GIL과 같은 정책이 나온 이유를 생각해 볼 수 있고 이러한 것들을 이해하는 과정이 가독성과 안정성을 모두 잡은 클린 코드를 만드는 발판이 되지 않을까한다.

추가적으로 GIL이 완벽하게 [Thread-safe를 보장하지는 않다고 한다.](https://stackoverflow.com/questions/1717393/is-the-operator-thread-safe-in-python) 그렇기 때문에, 개발이 편리하다는 파이썬의 장점을 뒤로하더라도 개발자로서 파이썬의 철학에 `명시적인 것이 암시적인 것보다 낫다(Explicit is better than implicit)` 써있듯이 명시적으로 Lock을 걸어주어 프로그램을 보호하는 습관이 필요하다.

------------------------------------------------------------
<a name="Removing-GIL">[1]</a> 많은 사람들이 GIL을 제거하기 위해 많은 연구를 했지만, 아직 GIL을 제거할 수 있는 방법은 없는 듯 하다. GIL을 제거하기 위해서는 싱글 쓰레드의 성능을 떨어뜨리지 않아야한다는 전제가 있는데 이것이 쉽지 않다고 한다.
<a name="GC-RC">[2]</a> Reference Counting에 대해 순환 참조(Reference Cycle)가 발생할 경우에는 Cyclic Garbage Collector를 별도로 사용한다고 한다.
