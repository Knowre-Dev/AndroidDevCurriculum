# Volatile

## 📌 Intro

- Java 의 volatile 키워드에 대해서 알아봅시다.

## 📌 volatile 이란

- 자바는 최적화를 위해 개발자가 선언한 변수를 메모리에서 읽어 올 때 이 값을 CPU 캐쉬에도 저장하게 됩니다.
- 한 번 그렇게 읽어 온 후에는 같은 변수에 접근 시 다시 메모리에서 읽어오지 않고 이전에 CPU 캐쉬에 저장해 두었던 값을 읽어 사용하게 됩니다.
- 만약 멀티 쓰레딩 환경에서 기존 A 쓰레드가 아닌 다른 B 쓰레드에서 그 값을 변경 시켰다면 A 쓰레드가 CPU 캐쉬로 접근해서 읽는 값과 B 쓰레드가 변경시켜 메모리에 저장한 값은 서로 다를 수 있기 때문에 변수 값이 불일치 하는 현상이 일어나게 됩니다.
- 이를 방지하기 위해 사용되는 변수가 volatile 키워드 입니다.
- volatile 키워드가 붙은 변수는 쓰레드가 값을 읽을 때 CPU 캐쉬로 저장해 읽어오지 않고 매번 메모리로 접근해 값을 읽어 옵니다.
- 즉, volatile 은 쓰레드 사이에서 변수의 값 변화에 대한 가시성을 보장해준다라고 할 수 있습니다.
- 위 내용은 요약내용이며 더 상세한 내용은 아래 링크에 잘 정리되어 있기 때문에 이 내용을 일독해보시길 권장드립니다.

![🇰🇷 한글 Reference](https://nesoy.github.io/articles/2018-06/Java-volatile)
![🇺🇸 영문 Reference](http://tutorials.jenkov.com/java-concurrency/volatile.html#when-is-volatile-enough)


## 📌 기타사항

- 위 레퍼런스에는 나와있지 않은 내용인데, 만약 멀티 쓰레드들이 공유하는 변수가 10개가 있고 이 10개 모두 변수의 값 변화에 대해 가시성을 보장해야 한다면 10개 모두 volatile 키워드를 붙여줘야 할지 의문이 들 수 있습니다.
- 결론은 그렇지 않습니다.
- 쓰레드가 volatile 변수를 수정해 메모리로 저장하면, 이 volatile 가 수정되기 전엔 같은 object 안에서 수정되었던 모든 변수들(volatile 여부 상관없이)을 메모리에 함께 저장합니다.
- 또한, 어떤 쓰레드가 volatile 변수를 메모리에서 읽어 들인다고 하면, 해당 volatile 변수가 이전에 수정되었다면, 그 수정이 일어났을 때 함께 저장됐었던 다른 변수들도 모두 읽어들이게 됩니다.
- 때문에 가시성이 보장되어야하는 모든 변수에 volatile 키워드를 붙일 필요 없이 개발자가 적절하게 최소한의 변수에만 volatile 키워드를 붙여 사용해도 모든 변수에 대해 가시성을 보장할 수 있습니다.
- 위와 같은 보장을 위해 volatile 키워드는 happened before 라는 성질을 갖고 있습니다. 예를들어 순차적인 두 이벤트 A -> B 가 있을 경우 이 순서는 변경되지 않음을 의미하는데, 자바는 컴파일러가 변수의 읽기/쓰기 과정을 개발자가 작성한 코드의 순서대로 진행하지 않고 최적화를 위해 그 순서를 변경하는 것이 가능한데 volatile 키워드를 붙이게 되면 이를 금지하게 됩니다. 
- 예를들어 아래와 같은 코드가 있다고 할 때

``` kotlin
sharedObject.nonVolatile1 = 1
sharedObject.nonVolatile2 = 2
sharedObject.nonVolatile3 = 3

sharedObject.volatile = "flush"
```

- 컴파일러는 volatile 변수 수정 이전의 nonVolatile 변수 수정은 서로 순서가 바뀔 수 있지만, nonVolatile 들의 변수 수정들은 반드시 volatile 변수 수정 명령이 실행되기 전에 실행됩니다.