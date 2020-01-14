# Profiler

<br>

## 📌 개요

Android studio 에서는 앱의 성능을 측정할 수 있는 Profiler 를 제공한다. (Android Studio 3.0+) 앱의 반응 속도가 느리거나, 배터리 소모가 많은 경우 등의 성능상에 문제가 있을 때 프로파일링을 통해 어디에서 문제를 유발하고있는지 추적할 수 있다. Android studio Profiler 에 대한 자세한 내용은 [공식 문서](https://developer.android.com/studio/profile)를 참고하면 된다.

Profiler 에서 추적할 수 있는 내용을 요약하면 아래와 같다.

- [CPU Profiler로 CPU 활성상태 검사](https://developer.android.com/studio/profile/cpu-profiler.html) : CPU 레코딩을 이용해 함수 콜스택을 확인할 수 있고 특정 메서드의 호출 시간까지 파악할 수 있다.
- [Memory Profiler로 Java heap과 메모리 할당 검사](https://developer.android.com/studio/profile/memory-profiler.html) : 이를 이용해 앱에서 메모리릭 여부를 판단할 수 있다.
- [Network Profiler로 트래픽 검사](https://developer.android.com/studio/profile/network-profiler.html)
- [Energy Profiler로 배터리 사용량 검사](https://developer.android.com/studio/profile/energy-profiler.html)

이번 장에서는 우선 CPU Profiler, Memory profiler 에 대해서만 다뤄볼 예정이다.

> Note: 요즘 하드웨어 성능이 아주 뛰어나서 너무 이상하게 짜지 않는 이상 웬만하면 성능 이슈는 발생하지 않을 것이다.(*아마도?*) 하지만 실세계(사용자 환경)는 우리가 미리 예측하지 못한 일들이 벌어지므로.. Profiler를 이용해 앱의 성능을 개선하여 모든 사용자가 만족할 수 있는 앱을 만들어보자!



## 📌 Next step

- [Memory dump](https://github.com/Knowre-Dev/AndroidDevCurriculum/blob/content/memory_dump/Android/Debugging/Profiler/MemoryDump/MemoryDump.md)

