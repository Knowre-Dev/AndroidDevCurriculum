## Array

배열은 동일한 타입을 가진 데이터들의 모음이다. 대부분의 언어가 배열을 지원하는만큼 배열은 중요한 자료구조이지만, 

- 배열을 생성할 때 크기를 지정해야만 하는 점
- 배열의 크기를 변경할 수 없다는 점

등은 프로그래밍에 있어 꽤나 불편한 일이다. 하지만, 데이터의 크기가 확정적인 상황이라면 배열은 그만큼 좋은 퍼포먼스를 낼 수 있는 장점도 있다. 

### 코틀린에서의 Array

코틀린에서 배열을 만드는 방법은 다양하다. 

- arrayOf함수에 원소를 넘긴다.
- arrayOfNulls 함수에 정수 값을 인자로 넘기면 모든 원소가 null이고 인자로 넘긴 값과 크기가 가은 배열을 만들 수 있다.
- Array생성자는 배열 크기와 람다를 인자로 받아서 람다를 호출하여 각 배열 원소를 초기화해준다. 

```kotlin
//배열을 만들 수 있는 코틀린 표준 라이브러리인 arrayOf, arrayOfNulls, emptyArray
val integerArray2 = arrayOf(1, 2, 3) //1, 2, 3

//Array 생성자를 이용하여 만드는 방법
val integerArray = Array(5) { i -> i * i } // 0, 1, 4, 9, 16

//BoxingOverHead 없이 primitive Type으로 배열을 생성할 수 있는 IntArray, ByteArray 
val integerArray3 = intArrayOf(1, 2, 3) //1, 2, 3 (int [] 와 같다.)
        
//intArrayOf는 IntArray 클래스를 생성하고 역시 람다로 생성자를 전달할 수 있음
val integerArray4 = IntArray(5) { i -> i * i } // 0, 1, 4, 9, 16
```

코틀린 표준 라이브러리는 배열의 기본 연산에 더해 컬렉션에 사용할 수 있는 모든 확장 함수를 배열에도 제공한다. (다만 이런 확장함수가 반환하는 값은 배열이 아니라 리스트)

