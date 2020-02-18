# ViewPager2

ViewPager2는 2019년 11월 20일에 **버전 1.0.0** 이 출시되었습니다.

## ViewPager와 차이점

기존 ViewPager의 PagerAdapter을 기반으로 구성되어 있어 PagerAdapter를 상속받은 Adapter를 구현해야 했지만
ViewPager2에서는 익숙한 RecyclerView로 구현되었기 때문에 RecyclerView의 사용법과 같다.

그렇기 때문에 RecyclerView의 사용법과 마찬가지로 RecyclerView.Adapter를 상속받은 adapter에 viewPager2 세팅하여 사용 가능하다.

ViewPager 
기존 ViewPager의 addPageChangeListener는 인터페이스여서 메서드를 모두 재정의해야 했다.

## 사용법
```
dependencies {
    implementation "androidx.viewpager2:viewpager2:${version}"
}
```
사용법은 [RecyclerView의 사용법](https://github.com/Knowre-Dev/AndroidDevCurriculum/blob/content/recyclerView/Android/ViewComponent/View/AdapterViews/RecyclerView/RecyclerView.md)과 같다.😜

### OnPageChangeCallback
페이지가 스크롤 또는 이동할 때마다 스크롤 상태나 위치, 몇 번째 페이지로 이동했는지를 알기 위해서 Listener를 등록 할 경우 

**기존 ViewPager**의 addPageChangeListener는 인터페이스여서 **메서드를 모두 재정의**해야 했다.

``` kotlin
view_pager.addOnPageChangeListener(object : ViewPager.OnPageChangeListener {
            override fun onPageScrollStateChanged(state: Int) {
            
            }

            override fun onPageScrolled(position: Int, positionOffset: Float, positionOffsetPixels: Int) {

            }
            override fun onPageSelected(position: Int) {

            }
        })
```
하지만 **ViewPager2**의 OnPageChangeCallback은 추상 클래스이기 때문에 **필요한 메서드만 재정의**하면 된다.
```
view_pager.registerOnPageChangeCallback(object : ViewPager2.OnPageChangeCallback() {
    override fun onPageSelected(position: Int) {
    
    }
})
```

### PageTransformer
![ZoomPageTransformer](https://i.stack.imgur.com/wY0Dj.gif)

페이지 이동시 애니메이션은 [PageTransformer](https://developer.android.com/training/animation/screen-slide-2#pagetransformer)를 이용하여 구현할 수 있습니다.

## 기타
ViewPager2는 따로 layoutManager를 설정할 수 없고 내부에 LinearLayoutManager로 구현되어 있다.
그렇기 때문에 orientation만 변경가능하다.
``` kotlin
view_pager.orientation = ViewPager2.ORIENTATION_HORIZONTAL

or

view_pager.orientation = ViewPager2.ORIENTATION_VERTICAL
```
