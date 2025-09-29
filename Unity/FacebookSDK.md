# 📌 Unity Facebook SDK 연동 가이드

Unity에서 Facebook SDK를 연동하여 광고 성과(CPI) 추적하는 방법

---

## 1. 사전 준비

**필요한 정보:**
- App Name (Facebook 콘솔 등록명)
- Facebook App ID (16자리 숫자)
- Client Token (영숫자 문자열)

---

## 2. SDK 임포트

1. https://developers.facebook.com/docs/unity/downloads 에서 최신 버전 다운로드
2. Unity에서 `Assets` → `Import Package` → `Custom Package`
3. 다운받은 .unitypackage 선택 → 전체 Import
4. `Assets/FacebookSDK/Examples` 폴더 삭제 (불필요)

---

## 3. Facebook 설정

1. Unity 메뉴: `Facebook` → `Edit Settings`
2. 입력:
   - App Name: Facebook 콘솔 등록명
   - App ID: 받은 16자리 숫자
   - Client Token: 받은 토큰
3. Android 체크 켜기, iOS 끄기
4. 저장 (Ctrl+S)

---

## 4. 초기화 코드

`Assets/Scripts/FacebookManager.cs` 생성:
```csharp
using UnityEngine;
using Facebook.Unity;

public class FacebookManager : MonoBehaviour
{
    void Awake()
    {
        if (!FB.IsInitialized)
        {
            FB.Init(OnInitComplete);
        }
        else
        {
            FB.ActivateApp();
        }
    }

    void OnInitComplete()
    {
        if (FB.IsInitialized)
        {
            FB.ActivateApp();
            Debug.Log("Facebook SDK 초기화 완료");
        }
    }

    void OnApplicationPause(bool pauseStatus)
    {
        if (!pauseStatus)
        {
            FB.ActivateApp();
        }
    }
}
```

---

## 5. Android Manifest 수정

`Assets/Plugins/Android/AndroidManifest.xml` 생성:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
  
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  
  <application>
    
    <meta-data 
        android:name="com.facebook.sdk.ApplicationId" 
        android:value="your_application_id" />
    
    <meta-data 
        android:name="com.facebook.sdk.ClientToken" 
        android:value="your_client_token" />
    
    <meta-data 
        android:name="com.facebook.sdk.AutoLogAppEventsEnabled" 
        android:value="true" />
    
    <meta-data 
        android:name="com.facebook.sdk.AdvertiserIDCollectionEnabled" 
        android:value="true" />
    
  </application>
</manifest>
```

---

## 6. 빌드 및 테스트

1. File → Build Settings → Android 선택
2. 첫 씬을 Scenes In Build에 추가
3. Build 클릭
4. APK를 Android 기기에 설치 후 실행
5. 1~5분 후 확인
- fb_mobile_activate_app 이벤트 확인
- 설치 수 증가 확인

---

### 측정 데이터

- 앱 설치 수 (CPI)
- DAU (일일 활성 사용자)
- 국가/기기 분포
