# 📌 Unity Game Analytics 연동 가이드

Unity에서 Game Analytics를 연동하여 Playtime, Retention 등을 자동 측정하는 방법

---

## 1. 계정 생성 및 게임 등록

1. https://gameanalytics.com 접속 → Sign Up
2. 로그인 후 `Create Game` 클릭
3. 입력:
   - Game Name: 게임 이름
   - Platform: Android 선택
   - Studio Name: 스튜디오명
4. 생성 후 **Game Key**와 **Secret Key** 복사 (나중에 사용)

---

## 2. SDK 다운로드 및 임포트

1. https://github.com/GameAnalytics/GA-SDK-UNITY/releases 접속
2. 최신 버전에서 `GameAnalytics.unitypackage` 다운로드 (**tar.gz 말고**)
3. Unity에서 다운받은 .unitypackage 파일 더블클릭
4. Import 창에서 전체 선택 → Import

---

## 3. Game Key 설정

1. Unity 메뉴: `Window` → `GameAnalytics` → `Select Settings`
2. Inspector에서 입력:
   - Game Key: 발급받은 32자리 키
   - Secret Key: 발급받은 40자리 키
3. Platform 설정:
   - Android 체크 켜기
   - iOS 체크 끄기
4. 저장 (Ctrl+S)

---

## 4. 초기화 코드

기존 FacebookManager.cs에 추가하거나 새 스크립트 생성:

### FacebookManager.cs에 추가하는 경우
```csharp
using UnityEngine;
using Facebook.Unity;
using GameAnalyticsSDK;

public class FacebookManager : MonoBehaviour
{
    void Awake()
    {
        // Facebook SDK 초기화
        if (!FB.IsInitialized)
        {
            FB.Init(OnInitComplete);
        }
        else
        {
            FB.ActivateApp();
        }
        
        // Game Analytics 초기화
        GameAnalytics.Initialize();
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

### 별도 스크립트로 만드는 경우
`Assets/Scripts/GameAnalyticsManager.cs`:

```csharp
using UnityEngine;
using GameAnalyticsSDK;

public class GameAnalyticsManager : MonoBehaviour
{
    void Awake()
    {
        GameAnalytics.Initialize();
        Debug.Log("Game Analytics 초기화 완료");
    }
}
```

---

## 5. 빌드 및 테스트

1. File → Build Settings → Android 선택
2. 첫 씬을 Scenes In Build에 추가
3. Build 클릭
4. APK를 Android 기기에 설치 후 실행 (5초 이상 유지)
5. 1~5분 후 확인: https://gameanalytics.com
- 게임 대시보드에서 "Waiting for data" → 데이터 표시로 변경
- DAU, Sessions, Session Length 확인

### 자동 측정 데이터
**초기화만 하면 자동으로 수집**:

- Session Start/End (자동)
- Playtime (세션 길이)
- DAU (일일 활성 사용자)
- Retention D1/D7/D30 (자동 계산)
- 국가/기기/OS 정보
