# Threading

## 📌 Intro

- 안드로이드에서는 다양한 Thread 사용방법이 존재한다.
- 어떠한 상황에 어떻게 사용하는지에 대해여 **✨기본적이지만 필수적으로 알아야하는✨** 내용들에 대해서만 알아본다.

## 📌 목표

- 기본적인 Thread 의 원리와 올바른 사용 방법을 숙지한다.(본 문서에서 다루는 목표, Handler 와 Executor Service 는 아래 링크 참고)
- 올바른 [Handler](https://github.com/Knowre-Dev/AndroidDevCurriculum/blob/master/Android/Threading/Handler/Handler.md) 사용법을 알 수 있다.
- [Executor Service](https://github.com/Knowre-Dev/AndroidDevCurriculum/blob/master/Android/Threading/ExecutorService/ExecutorService.md) 에 대해 알 수 있다.

## 📌 기본적인 Thread 의 사용

- Thread 를 사용함에 앞서 InterruptedException 에 대해 이해할 필요가 있다.
- 보통, Interrupt 는 이 Interrupt 를 요청하는 Thread 에게 멈추라는 신호이며 이 신호를 Thread 가 받았을 때 발생하는 exception 이 InterruptedException 이다. (하나의 Thread 는 다른 Thread 를 즉각적으로 중지할 권한이 없다. 다만, 중지 할 것을 요청하는 interrupt 요청만 가능하다. 실제로 중지할지 말지는 요청 당하는 Thread 의 InterruptedException 에서 결정한다.)
  - **주의할 점은 Thread 가 pause 상태일때만 이 Interrupt 가 유효하다는 것이다.**
  - Thread 가 Running 상태일 경우에는 interrupt 가 발생해도 InterruptedException 을 발생시키지 않고 Thread 가 미래에 일시 정지 상태가 되면 InterruptedException 을 발생시킨다. **(InterruptedException 이 발생한 후 interrupt state 는 clear 된다.)**
- 이 InterruptedException 을 처리하는 방법의 표준은 없지만 보통 해당 Thread 를 멈추라는 신호로 처리되며 이에 맞는 작업이 이루어져야한다.
(e.printStackTrace() 만하면 보통은 잘못된 처리이다. 차라리 앱이 죽는것이 나을 수도 있지만 앱이 크래쉬 되지도 않는다.)
- interrupt 요청은 {Target Thread}.interrupt() 로 한다.
- {Target Thread}.interrupt() 하게되면 그 순간에는 interrupt state 가 true 로 설정되지만, 현재 Blocking, Pause 이거나 미래에 Blocking, Pause 되면 Interrupt Exception 이 발생되고 이후에 interrupt state 가 다시 false 가 된다.
  - 때문에, 이 state 를 유지할 필요가 있을 때는 아래와 같은 작업이 이루어져야 한다.
  ```kotlin
  try {
    // ..
  } catche(e: InterruptException) {
    Thread.currentThread().interrupt()
    // ..
  }
  ```
- Thread 가 interrupt 되었는지 확인하는 방법은 아래와 같이 두가지 방법이 있다.
  - Thread.interrupted()
    - 정적 메소드이며, 현재 Thread 의 interrupt state 를 반환 하고 interrupt state 를 clear 한다.(false)
  - {Target Thread}.isInterrupted()
    - 인스턴스 메소드이며, 현재 Thread 의 interrupt state 를 반환만 한다.

- Thread 를 정확히 사용하지 않은 상태로 개발하게되면 보통 개발자들은 아래와 같이 코드하여 작업한다.

``` kotlin
Thread {  
  //do something
  
  Thread.sleep(millis)
  
  //do something
}
  .start()
```

- blocking 이 필요없이 짧은 시간안에 끝나는 작업에는 위와 같이 InterruptedException 처리가 필요없지만 위와 같이 Thread.sleep(), Thread.join(), BlockingQueue.take(), Future.get(), Object.wait() 와 같이 Blocking 된 코드가 존재할 경우 InterruptedException 의 적절한 처리가 필요하다.  
- 또한, Thread 안에서 특정 조건을 만족하거나 어떤 값이 리턴될 때까지 계속해서 반복되는 작업을 해야할 경우에도 InterruptedException 의 적절한 처리가 필요하다. 
- Thread 작업이 끝난 후 ui 업데이트가 필요한 경우에는 Thread 를 Extends 해서 ui 의 weak reference 를 받아 별도로 작업해야한다.(이는 아래에서 알아보자)


``` kotlin
Thread {
  while (true) {
    //do somthing
    
    Thread.sleep(millis)
    
    //do somthing
    
    //do somthing
    
    if (someCondittion) {
      break
    }
  }
}
  .start()
```

- 위와 같은 경우 정상적인 경우에는 잘 실행되고 종료도 잘되겠지만 interrupt 요청을 받았을 때는 정상적으로 Thread 가 종료되지 않는다. (앱 크래쉬)
- 때문에 아래와 같이 interrupted 를 체크해 작업하는 방식으로 바꾸는 것이 좋다.

``` kotlin
Thread {
  while(!Thread.currentThread().isInterrupted) {
    //bloiking 작업
  }
}
  .start()
```

- 하지만 위 역시도 InterruptedException 에 대한 처리가 안 되어 있다.
- 이 역시 Thread 안에서 블록킹 작업이 있을 경우 InterruptedException 을 적절히 처리해줘야한다.
- InterruptedException 안에서 e.printStackTrace() 코드만 남겨두도록 하지 않는다.
- 아래와 같이 e.printStackTrace() 만 남겨두었을 경우 어떤일이 일어나는지 확인해보자.

``` kotlin
val sleepThread = Thread {
  while (!Thread.currentThread().isInterrupted) {
    try {
      Thread.sleep(1000)
    } catch (e: InterruptedException) {
      e.printStackTrace()
    }
  }
}

sleepThread.start()

Thread.sleep(500)

sleepThread.interrupt()
```

- 위의 경우 
  - sleepThread 를 시작한 후 500ms 뒤에 sleepThread.interrupt 로 sleepThread 로 interrupt 신호를 보냄
  - sleepThread 는 Thread.sleep(1000) 로 blocking 된 상태이므로 interrupt 에 반응해 InterruptedException 이 발생됨
  - InterruptedException 의 catch 문이 정의되어 있으므로 e.printStackTrace() 가 실행됨
  - 문서 앞 부분에서 설명한 내용({Target Thread}.interrupt() 하게되면 interrupt state 가 true 로 설정되지만, 현재 Blocking, Pause 이거나 미래에 Blocking, Pause 되면 Interrupt Exception 이 발생되고 이후에 interrupt state 가 다시 false 가 된다.)에 따라 InterruptedException 이 발생하고 난 후 interrupt statue 가 false 가 됨
  - while 문 검사에서 위 내용에 따라 Thread.currentThread().isInterrupted 가 false 이므로 다시 while 문 안이 실행이 됨.
  - 앱 크래쉬가 나지도 않으면서 Thread 는 종료되지 않음.
- 때문에 InterruptedException 안에서는 아래 두가지의 경우와 같이 명시적으로 적절한 처리를 해줘야 한다.


``` kotlin
1️⃣
val sleepThread = Thread {
  while (!Thread.currentThread().isInterrupted) {
    try {
      Thread.sleep(1000)
    } catch (e: InterruptedException) {
      break
    }
  }
}

sleepThread.start()

Thread.slepp(500)

sleepThread.interrupt()
```

``` kotlin
2️⃣
val sleepThread = Thread {
  while (!Thread.currentThread().isInterrupted) {
    try {
      Thread.sleep(1000)
    } catch (e: InterruptedException) {
      Thread.currentThread().interrupt() /** 명시적으로 interrupt state 를 true 로 설정 (interrupt 값 유지) */
    }
  }
}

sleepThread.start()

Thread.slepp(500)

sleepThread.interrupt()
```

- 1️⃣, 2️⃣ 은 Thread interrupt 에 의해 종료될 경우 종료 후의 interrupt state 가 다르다는 것을 명심해야한다. 
- 종료되고 난 후에 interrupt state 에 따른 행동이 있을 경우 그 상황에 맞는 방법을 택해야할 것이다.
- 기본적으로는 2️⃣ 번으로 코딩하는 습관을 갖자.
- UI 변경 작업이 필요한 경우는 아래와 같은 방법으로 Thread 를 extends 해서 실행하자.

``` kotlin 
internal class MyLayout constructor(
        context: Context,
        attrs: AttributeSet? = null

) : FrameLayout(context, attrs) {

    class TaskThread constructor(private val weakView: WeakReference<MyLayout>) : Thread() {
        override fun run() {
            while (!Thread.currentThread().isInterrupted) {
                try {
                    Thread.sleep(1000) //이 곳에 하고자 하는 작업을 명세, 해당 작업이 Blocking 작업이라하더라도 정상적으로 처리 됨.

                    val layout = weakView.get()
                    
                    layout?.let { 
                        (layout.context as Activity).runOnUiThread { 
                            layout.updateUi() //** updateUi 안에 runOnUiThread 를 넣어도 상관 없으며 Handler 로 처리해도 무방 */
                            
                            //layout.main.post { layout.updateUi()}
                        }
                    }
                } catch (e: InterruptedException) {
                    this.interrupt()
                }
            }
        }
    }

    private val taskThread = TaskThread(WeakReference(this))

    private val main = Handler(Looper.getMainLooper())
    
    init {
        LayoutInflater.from(context).inflate(R.layout.view_my, this, true)
    }

    override fun onDetachedFromWindow() {
        super.onDetachedFromWindow()

        /** 
        * view 가 detach 될 경우 thread 를 interrupt 해 종료 시켜 준다. 
        * adapter view 안에 사용되는 view 일 경우에는 이와는 다르게 처리해줘야 할 수도 있다. 
        */
        taskThread.interrupt() 
    }

    fun startTask() {
        taskThread.start()
    }

    fun clearTask() {
        /** view 외부에서도 task 를 interrupt 할 수 있도록 api 를 만들어 두어도 좋다. */
        taskThread.interrupt()
    }
    
    fun updateUi() {
        //UI Update
    }
    
}
```
