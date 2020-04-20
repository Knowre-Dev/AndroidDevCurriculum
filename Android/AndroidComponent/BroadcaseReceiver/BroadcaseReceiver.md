# 브로드캐스트 수신
앱은 두 가지 방식으로, 즉 **manifest에 선언된 수신자** 및 **컨텍스트에 등록된 수신자를 통해 브로드캐스트**를 수신할 수 있습니다.

## manifest에 선언된 수신자 (정적 BroadCast Receiver 등록)
참고: 앱이 API 레벨 26 이상을 타겟팅하면 manifest를 사용하여 암시적 브로드캐스트(앱을 구체적으로 타겟팅하지 않는 브로드캐스트)의 수신자를 선언할 수 없습니다. 단, 제한이 면제되는 몇 가지 암시적 브로드캐스트는 예외입니다. 대부분의 상황에서 예약된 작업 을 대신 사용할 수 있습니다. (예외: https://developer.android.com/guide/components/broadcast-exceptions)

``` xml
    <receiver android:name=".MyBroadcastReceiver"  android:exported="true">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED"/>
            <action android:name="android.intent.action.INPUT_METHOD_CHANGED" />
        </intent-filter>
    </receiver>  
```

## 컨텍스트에 등록된 수신자 (동적 BroadCast Receiver 등록)

https://developer.android.com/guide/components/broadcasts#context-registered-receivers
