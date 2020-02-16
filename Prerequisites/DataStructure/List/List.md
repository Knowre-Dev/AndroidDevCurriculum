# Collection Framework

자바와 코틀린은 클래스와 인터페이스 집합으로 이루어진 Collection 프레임워크를 제공하여 **객체들의 그륩을 단일 유닛**으로 나타낼 수 있다.



### Java Collection Framework

![collection framework](https://www.sitesbay.com/collection-framework/images/collection-framework-hierarchy.png)



### Kotlin Cellection Framework

![Collection interfaces hierarchy](https://kotlinlang.org/assets/images/reference/collections-overview/collections-diagram.png)



Collection 과 Map은 자바 자료구조를 이루는 최상단 인터페이스이며 여기서는 Collection 인터페이스를 구현하는 **List, Queue, Set**을 알아보자. 

## Array

리스트를 다루기 전에 배열에 대해서 알아보자. 배열은 동일한 타입을 가진 데이터들의 모음이다. 대부분의 언어가 배열을 지원하는만큼 배열은 중요한 자료구조이지만, 

- 배열을 생성할 때 크기를 지정해야만 하는 점
- 배열의 크기를 변경할 수 없다는 점

등은 프로그래밍에 있어 꽤나 불편한 일이다. 하지만, 데이터의 크기가 확정적인 상황이라면 배열은 그만큼 좋은 퍼포먼스를 낼 수 있는 장점도 있다. 

### 코틀린에서의 Array

- 코틀린에서 Array를 만드는 여러가지 방법

  ```kotlin
          //1.Array 생성자를 이용하여 만드는 방법
          val integerArray = Array(5) { i -> i * i } // 0, 1, 4, 9, 16
  
          //2.배열을 만들 수 있는 코틀린 표준 라이브러리인 arrayOf, arrayOfNulls, emptyArray
          val integerArray2 = arrayOf(1, 2, 3) //1, 2, 3
  
          //3. BoxingOverHead 없이 primitive Type으로
          // 배열을 생성할 수 있는 IntArray, ByteArray와 같은 클래스가 있다. 
          val integerArray3 = intArrayOf(1, 2, 3) //1, 2, 3 (int [] 와 같다.)
          
          //4. intArrayOf는 IntArray 클래스를 생성하고 역시 람다로 생성자를 전달할 수 있음
          val integerArray4 = IntArray(5) { i -> i * i } // 0, 1, 4, 9, 16
  ```

## List 

- Immutable vs mutable
- Constructing Collections
- Ranges and Progression
- Sequence
- 

