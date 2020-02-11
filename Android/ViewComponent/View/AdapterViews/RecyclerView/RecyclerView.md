# RecyclerView

## 개요

RecyclerView 는 앱에서 대량의 데이터 세트 또는 자주 변경되는 데이터에 기반한 요소의 스크롤 목록을 표시해야 할 때 사용하면 됩니다.
RecyclerView 는 5.0(롤리팝) 버전에서 함께 발표되었고 ListView 보다 더 진보하고 유연해진 버전이라고 소개하고 있습니다.

리스트안에 뷰는 ViewHolder 객체로 표현됩니다. 이러한 객체는 RecyclerView.ViewHolder를 확장하여 정의한 클래스의 인스턴스입니다. 각 ViewHolder는 뷰를 사용하여 단일 항목을 표시하는 역할을 합니다.

ViewHolder 객체는 RecyclerView.Adapter를 확장하여 만든 어댑터에서 관리합니다. 어댑터는 필요에 따라 뷰 홀더를 만듭니다. 또한 어댑터는 뷰 홀더를 데이터에 바인딩합니다.

| Class              | Description   |
| ------------------ |---------------|
| **Adapter**        | 기존의 ListView에서 사용하는 Adapter와 같은 개념으로 데이터와 아이템에 대한 View생성 |
| **ViewHolder**     | 재활용 View에 대한 모든 서브 뷰를 보유 |
| **LayoutManager**  | 아이템의 항목을 배치 |
| **ItemDecoration** | 아이템 항목에서 서브뷰에 대한 처리 |
| **ItemAnimation**  | 아이템 항목이 추가, 제거되거나 정렬될때 애니메이션 처리 |

## ListView 와 차이점

RecyclerView와 ListView의 가장 큰 차이점은 Layout Manager와, View Holder 패턴의 의무사용, Item에 대한 뷰의 변형이나 애니메이션할 수 있는 개념이 추가되었습니다. 리스트 뷰의 성능 상의 이슈도 해결해주면서, 많은 타입의 뷰들을 가독성 있게 보여줍니다.

<p align="center"><img src="https://github.com/Knowre-Dev/AndroidDevCurriculum/blob/content/recyclerView/Android/ViewComponent/View/AdapterViews/RecyclerView/recyclerView_listView.png?raw=true"></p>

## 사용법
RecyclerView 위젯에 액세스하려면 다음과 같이 프로젝트에 v7 지원 라이브러리를 추가해야 합니다.

1. 앱 모듈의 build.gradle 파일 열기
2. dependencies 섹션에 지원 라이브러리 추가
<pre>
dependencies {
    implementation 'com.android.support:recyclerview-v7:${version}'
}
</pre>

### RecyclerView.Adapter

### RecyclerView.ViewHolder


## 주의사항

https://www.androidhuman.com/lecture/kotlin/2017/11/26/kotlin_android_extensions_on_viewholder/

참조:<br/>
https://developer.android.com/guide/topics/ui/layout/recyclerview,
https://armful-log.tistory.com/27,
https://itmining.tistory.com/12,
https://www.androidhuman.com/lecture/kotlin/2017/11/26/kotlin_android_extensions_on_viewholder/
