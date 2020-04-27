

[TOC]

<br>

# ViewGroup

기초적인 내용은 안다는 가정 하에 안드로이드에서 UI를 구성할 때 필요한 ViewGroup 에 대하여 다뤄본다.

<br>

```
<details>
<summary>기초적인 내용은 여기서 알아보기</summary>
<div markdown="1">

- [Android Developers - layouts](https://developer.android.com/guide/topics/ui/declaring-layout)
- [<안드로이드 뷰, 뷰 그룹, 레이아웃> 개념 정리](https://mattlee.tistory.com/74)

</div>
</details>
```

<br>

## 📌 ViewGroup 의 종류와 LayoutParams

### 👉🏻  자주 사용하는 ViewGroup 종류

| 종류                                                         | 설명                                                         | 사용 예                                                      | 주의사항                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| FrameLayout                                                  | 여러 뷰를 **겹쳐서** 배치할 때 사용하는 레이아웃             | Fragment 컨테이너                                            |                                                              |
| LinearLayout                                                 | 뷰를 **가로, 세로방향**으로 순서대로 배치할 수 있는 가장 기본적인 레이아웃 |                                                              | Orientation 설정 필수                                        |
| ✨ ConstraintLayout                                           | 배치하려는 **뷰 간에 제약**을 설정하여 복잡한 화면을 flat 하게 구성할 수 있는 레이아웃 |                                                              | ConstraintLyaout 중첩 시 성능 문제 발생 여지 있음            |
| [CoordinatorLayout](https://developer.android.com/reference/android/support/design/widget/CoordinatorLayout) | FrameLayout 의 슈퍼파워(?)버전으로, 자식뷰와 상호작용하기 위해 사용되는 레이아웃 | CollapsingToolbar                                            |                                                              |
| ScrollView                                                   | **스크롤**을 가능하게하여 화면 밖에도 뷰를 그릴 수 있는 레이아웃 |                                                              | 루트 안에 단일 뷰그룹만 포함할 수 있음                       |
| ✨RecyclerView                                                | 다양한 애니메이션 처리 및 커스텀이 용이하고, 뷰를 재사용하여 메모리 문제를 해결한 ListView 의 업그레이드 버전 |                                                              | ViewHolder 에서 Kotlin extension 사용시 주의사항 [참고](https://www.androidhuman.com/lecture/kotlin/2017/11/26/kotlin_android_extensions_on_viewholder/) |
| ViewPager                                                    | 데이터를 페이지 단위로 표시하고 화면을 스와이프하여 전환 가능한 컨테이너. ViewPager2 부터 RecyclerView 기반으로 만들어짐 | 페이지별로 수명주기를 관리해야하고자 할 때  Fragment 와 함께 사용 |                                                              |

<br>

### 👉🏻 LayoutParams

View 클래스에는 `getWidth()` 와 같은 메서드를 제공하지만, `setWidth()` 와 같은 메서드는 제공하지 않는다. 이유는 안드로이드에서 뷰의 크기 및 배치는 해당 View를 감싸고있는 ViewGroup 에 의해 결정되기 때문이다.

이렇게 View 자체 속성이 아닌 상위 레이아웃의 영향을 받는 속성들은 LayoutParams 에 정의되어있다. xml 에서 View 를 정의할 때 'layout_' 이라는 suffix 가 붙어있는 속성들이 LayoutParams 의 속성들이다. 각 ViewGroup 마다 자신만의 LayoutParams 를 가지고 있으며, 아래와 같이 하위 View 에서는 자신을 감싸는 ViewGroup.LayoutParams 를 정의하여 자신이 어떻게 측정되고 위치를 정할지 요청할 수 있다.

![img](https://developer.android.com/images/layoutparams.png)

따라서 코드로 View 의 크기 및 위치를 동적으로 변경하기 위해서는 해당 View 를 감싸고있는 ViewGroup.LayoutParams 를 이용해 바꿔주어야한다.

<br>

## 📌 ViewGroup이 View를 그리는 과정

뷰는 포커스를 얻으면 레이아웃을 그리도록 요청하는데, 이때 레이아웃의 계층구조 중 Root View(ViewGroup)를 제공해야한다. 따라서 루트노드에서 시작되어 트리를 따라 전위 순회 방식으로 그려진다. 부모 뷰는 자식 뷰가 그려지기 전에(즉, 자식 뷰 뒤에) 그려지며 형제 뷰는 전위 순회 방식에 따라 순서대로 그려진다. 

부모뷰에서 차례대로 자식뷰의 `draw()` 메서드를 호출하여 자식에게 그리기를 요청하고, 각 뷰는 자체적으로 드로잉을 담당한다.

> 그래서 ViewGroup 은 자기 자신에 대해 그릴게 없으므로, View 처럼 다시 그려져야할 경우에도 `onDraw()` 메서드가 호출되지 않는다. 따라서 뷰 그룹을 다시 그리도록 요청하기 위해서는 `dispatchDraw()` 를 오버라이드하거나 `setWillNotDrawEnabled(false)` 메서드로 설정을 바꿔줘야한다.

레이아웃을 그리는 과정은 뷰의 크기를 결정하는 **측정(measure)**단계와 어디에 배치할지 결정하는 **레이아웃(layout)**단계를 거쳐 그려진다. 

### 👉🏻 Measure pass

부모뷰에서 자식뷰들을 경유하며 수행되며, 뷰의 크기를 알아내기 위해 호출된다. 실제 크기 측정은 내부에서 `onMeasure(int, int)` 메서드를 호출하여 뷰의 크기를 알아낸다. 측정 과정에서는 부모뷰와 자식뷰간의 크기 정보를 전달하기 위해 `ViewGroup.LayoutParams` 클래스와 `ViewGroup.MeasureSpec` 클래스를 사용한다.

- ViewGroup.LayoutParams : 자식뷰 자신이 어떻게 측정되고 위치를 정할지 부모뷰에 요청할 때
- ViewGroup.MeasureSpec : 부모 뷰가 자식뷰에게 요구사항을 전달할 때

### 👉🏻 Layout pass

부모뷰에서 자식뷰들을 경유하며 수행되며, 뷰와 자식뷰들의 크기와 위치를 할당할 때 사용된다. `measure(int, int)`에 의해 각 뷰에 저장된 크기를 사용하여 위치를 지정한다. 내부적으로 `onLayout()`를 호출하고 `onLayout()`에서 실제 뷰의 위치를 할당하는 구조로 되어있다.

> `measure()`와 `layout()`메서드는 내부적으로 각각 `onMeasure()`와 `onLayout()` 메서드를 호출한다. 이것은 final로 선언된 `measure()`와 `layout()` 대신 `onMeasure()`와 `onLayout()`을 재정의할 것을 장려하기 위해서이다.

뷰의 `measure()`함수가 반환할때, 뷰의 `getMeasureWidth()`와 `getMeasureHeight()`값이 설정된다. 만약 자식 뷰 측정값의 합이 너무 크거나 작을 경우 다시 `measure()`함수를 호출하여 크기를 재측정한다.

뷰를 그리는 과정에 대한 자세한 내용은 [[안드로이드] 뷰가 그려지는 과정](https://namsieon.com/339) 에서 확인할 수 있다.

<br>