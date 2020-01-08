# Memory dump



## 📌 개요

Java(+Kotlin)는 JVM의 GC를 이용해 메모리를 관리하므로 C/C++처럼 개발자가 명시적으로 메모리 관리를 할 필요가 없다. GC는 나름 똑똑하지만, 안타깝게도 모든 상황에서 완벽하게 메모리를 정리하지 못해서 OOM이 발생하여 앱이 종료되어버린다. 안드로이드 세계에서는 이렇게 메모리를 정리하지 못하는 상황을 메모리 누수(memory leak)가 일어났다고 말한다.

<br>

## 📌 목표

- 대표적인 안드로이드 앱의 메모리릭에 대해 알아본다.
- Memory profiler를 이용해 메모리릭을 해결하는 방법에 대해 알아본다.

<br>

## 📌 Memory leak

앱에서 더 이상 사용되지 않는 메모리인데도, 아직 사용중인 메모리로 판단되어 GC의 정리 대상이 되지 못한 경우 메모리릭이 발생할 수 있다. GC의 정리 대상으로 선정하는 자세한 내용은 [여기](https://github.com/Knowre-Dev/AndroidDevCurriculum/blob/master/Prerequisites/Jvm/Memory/GarbageCollection/GarbageCollection.md)에서 알아보자.

안드로이드 세계에서는 액티비티와 같은 `context` 가 누수될 경우, OOM 발생 가능성이 매우 높아진다(가진 resource 가 너무 많은데다가 가리키고 있는 참조가 많기 때문에). 다행히 액티비티는 생명주기 콜백이 있어서 액티비티가 사라지기 전 `onDestroy()`에서 정리하면 되지만, 해당 context가 Strong reference로 연결되어있다면 액티비티가 종료되었음에도 불구하고 GC가 메모리를 회수할 수 없게된다.

하지만 메모리릭이 있다고해서 무조건 OOM이 발생하는 것은 아니다. 안드로이드 운영체제의 프로세스 캐시 정책에 따라 메모리를 정리하기때문에 OOM이 발생하지 않을 수도 있기 때문이다. **<u>그렇다고 메모리릭 파티가 열렸는데 안주하면 안된다.</u>** GC가 너무 빈번하게 일을 하면 앱의  성능이 나빠질 수 있기 때문이다(a.k.a stop the world).

### ⚠️ 안드로이드에서 메모리릭이 발생할 수 있는 상황

1. 액티비티에서 전역 static 객체를 참조하고있을 때
2. Strong reference를 가진 스레드가 호출한 액티비티보다 오래 유지되어있을 때

자세한 케이스는 구글링을 통해 참고해보자.

<br>

## 📌 Memory profiler

안드로이드 앱에서 메모리릭이 발생했는지 판단하는 방법으로 [LeakCanary](https://square.github.io/leakcanary/)를 활용할 수 있지만, 이번 장에서는 Android studio의 memory profiler를 활용하여 메모리릭을 해결해보자.

먼저 [공식문서](https://developer.android.com/studio/profile/memory-profiler)를 참고하여 Memory profiler를 사용하는 방법에 대해 숙지하고, [메모리릭 예제 프로젝트](https://github.com/Onedelay/MemoryLeakExample)를 fork 하여 메모리릭을 해결해보면 된다.