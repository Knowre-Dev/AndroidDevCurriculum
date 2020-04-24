# 브로드캐스트 수신
앱은 두 가지 방식으로, 즉 **manifest에 선언된 수신자** 및 **컨텍스트에 등록된 수신자를 통해 브로드캐스트**를 수신할 수 있습니다.

## manifest에 선언된 수신자 (정적 BroadCast Receiver 등록)
참고: 앱이 Android 8.0 이상을 타겟팅하면 manifest를 사용하여 대다수 암시적 브로드캐스트(앱을 구체적으로 타겟팅하지 않는 브로드캐스트)의 수신자를 선언할 수 없습니다. 대부분의 상황에서 예약된 작업을 대신 사용할 수 있습니다. [예외](https://developer.android.com/guide/components/broadcast-exceptions)

``` xml
<receiver android:name=".MyBroadcastReceiver"  android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="android.intent.action.INPUT_METHOD_CHANGED" />
    </intent-filter>
</receiver>  
```

## 컨텍스트에 등록된 수신자 (동적 BroadCast Receiver 등록)

1. BroadcastReceiver 인스턴스를 생성합니다.

``` kotlin
val receiver: BroadcastReceiver = MyBroadcastReceiver()
```

2. 다음과 같이 IntentFilter를 생성하고 registerReceiver(BroadcastReceiver, IntentFilter)를 호출하여 수신자를 등록합니다.
``` kotlin
val filter = IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION).apply {
    addAction(Intent.ACTION_AIRPLANE_MODE_CHANGED)
}

registerReceiver(br, filter)

```

[참고](https://developer.android.com/guide/components/broadcasts#context-registered-receivers)

## 시스템에서 수신하는 각 브로드캐스트를 처리

``` kotlin
private const val TAG = "MyBroadcastReceiver"

class MyBroadcastReceiver : BroadcastReceiver() {

    override fun onReceive(context: Context, intent: Intent) {
        StringBuilder().apply {
            append("Action: ${intent.action}\n")
            append("URI: ${intent.toUri(Intent.URI_INTENT_SCHEME)}\n")
            toString().also { log ->
                Log.d(TAG, log)
                Toast.makeText(context, log, Toast.LENGTH_LONG).show()
            }
        }
    }
}
```

## 암시적 BroadcastReceiver Intent 전송

``` kotlin
val intent = Intent().apply {
    setPackage(PACKAGE_NAME)
    action = ACTION_NAME
}

sendBroadcast(intent)
```

Android 4.0 이상에서는 브로드캐스트를 전송할 때 Intent의 **setPackage를 이용하여 패키지를 지정하여 전송**할 수 있습니다.

## 주의, 고려사항
**1. BroadcastReceiver에서 장기 실행 백그라운드 스레드를 시작해서는 안 됩니다.**

수신자의 onReceive(Context, Intent) 메서드는 기본 스레드에서 실행되므로 이 메서드의 실행 및 반환은 신속해야 합니다.<br/>
장기 실행 작업을 할 필요가 있다면 스레드 생성 또는 백그라운드 서비스 시작에 주의해야 합니다. onReceive() 반환 후 시스템이 프로세스 전체를 종료할 수 있기 때문입니다. 

**2. 민감한 정보나 악성 Broadcast를 수신받지 않기 위해서 주의해야 합니다.**

    - 브로드캐스트를 전송할 때 권한을 지정할 수 있습니다.
    - manifest에 선언된 수신자라면 manifest에서 android:exported 속성을 'false'로 설정할 수 있습니다. 그러면 수신자는 앱 외부 소스의 브로드캐스트를 수신하지 않습니다.
    - LocalBroadcastManager를 사용하여 수신을 로컬 브로드캐스트만으로 제한할 수 있습니다.

**3. manifest 선언보다 컨텍스트 등록을 사용하는 것이 좋습니다.**

여러 앱이 자체 manifest에서 동일한 브로드캐스트를 수신하도록 등록하면 기기 성능과 사용자 환경 모두에 상당한 영향을 줄 수 있습니다.<br/>
이런 상황을 방지하려면 manifest 선언보다 컨텍스트 등록을 사용하는 것이 좋습니다.
