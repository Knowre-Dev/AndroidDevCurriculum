# Collection

자바와 코틀린은 클래스와 인터페이스 집합으로 이루어진 **Collection** 프레임워크를 제공하여 객체들을 하나의 레퍼런스로 쉽게 표현할 수 있다. 자바의 Collection 프레임워크 사진을 먼저 살펴보자.

### Java Collection Framework

![collection framework](https://www.sitesbay.com/collection-framework/images/collection-framework-hierarchy.png)



자주 쓰는 ArrayList의 상속 구조를 살펴보면, **최상위 인터페이스 Iterable**이 있고, Collection 인터페이스가 Iterable을 구현하고 있다. List, Queue, Set인터페이스는 다시 Collection을 확장하고 있고, ArrayList 클래스는 바로 List의 구현체이다. 자주 쓰이는 Collection 프레임워크의 상속 구조 정도는 알아두면 좋을 것이다.

코틀린에서는 Collection Framework를 어떻게 제공할까? 코틀린의 공식 홈페이지에서 가져온 Collection 상속 구조를 살펴보자.

### Kotlin Cellection Framework

<img src="https://kotlinlang.org/assets/images/reference/collections-overview/collections-diagram.png" alt="Collection interfaces hierarchy" style="zoom:67%;" />



List까지는 동일하지만, 코틀린 Collection 프레임워크에서는 **MutableIterable이나 MutableCollection**과 같은 새로운 인터페이스가 추가 되었다. 한 가지 기억해야할 중요한 사실을 아래 코드로 확인해보자.

```kotlin
internal class CollectionTest {
    @Test
    fun collectionTest() {
        val list = arrayListOf(1,2,3)
        println(list.javaClass)

        val set = setOf(1,2,3)
        println(set.javaClass)
    }
}
```

결과는 **class java.util.ArrayList** 와 **class java.util.LinkedHashSet** 이다. 코틀린은 자체 Collection이 따로 없으며, 표준 자바 Collection을 활용하여 자바 코드와 상호작용이 원활하도록 설계되어 있다. 이는 코틀린에서 자바 함수를 호출할 때 코틀린의 Collection을 변환활 필요가 없다는 뜻이기도 하다. 

## Mutable vs Immutable 

코틀린 Collection을 자바 Collection을 나누는 가장 중요한 기준은 코틀린은 **Collection 안의 데이터에 접근하는 인터페이스와 Collection안의 데이터를 변경하는 인터페이스를 분리했다는 것이다.** 이 구분은 Kotlin.collections.Collection에서부터 시작한다. 

MutableCollection은 Collection을 확장(상속)하면서 원소를 추가,삭제 등을 할 수 있다. 

| Collection | MutableCollection |
| ---------- | :---------------- |
| size       | add()             |
| iterable() | remove()          |
| contains() | clear()           |

그런데 코틀린의 Collection은 자바의 Collection과 동일하다면서 어떻게 Mutable과 Immutable을 구분할 수 있는걸까?



## 코틀린 Collection과 자바

모든 코틀린의 Collection은 그에 상응하는 자바 Collection 인스턴스라는 점은 사실이다. 따라서 코틀린과 자바를 오갈 때 아무 변환도 필요없으며 래퍼 클래스를 만들 필요는 없다. 하지만 위에 코틀린 Collection 프레임워크 사진에서 보듯, 코틀린은 읽기 전용 인터페이스와 변경 가능한 두 가지 인터페이스를 제공한다. 이 두 가지 인터페이스는 자바 Collection 인터페이스를 그대로 옮겨와서 만든것이다. <img src="/Users/ukhyuns/knowre/AndroidDevCurriculum/Prerequisites/DataStructure/List/image-20200304003135470.png" alt="image-20200304003135470" style="zoom:80%;" /> 

위 그림은 자바 표준 클래스를 코틀린에서 어떻게 취급하는지를 볼 수 있다. **ArrayList가 마치 MutableList 인터페이스를 상속받는것처럼 취급한다.** 이런 방식을 통해 코틀린은 자바의 Collection과의 호환성을 잃지 않으면서도 읽기 전용과 쓰기 전용 인터페이스를 분리한다.

이런 성질로 인해서 발생하는 중요한 이슈가 있다. 자바 코드에서 Collection을 파라미터로 받아서 추가,삭제하는 함수가 있다고 가정해보자. 이 때 코틀린의 List를 넘기면 리스트의 내용이 변한다! 자바의 컴파일러는 읽기 전용과 쓰기 전용을 구분하지 않으므로, 코틀린에서 만든 읽기 전용 Collection 으로 선언된 객체라도 자바 코드에서는 Collection 내용을 변경할 수 있는 것이다. 따라서 개발자가 컬렉션을 자바로 넘기는 코틀린 프로그램을 작성한다면 호출하려는 자바 코드가 컬렉션을 변경할지 여부에 따라 올바른 파라미터 타입을 사용할 책임은 개발자에게 있다.

