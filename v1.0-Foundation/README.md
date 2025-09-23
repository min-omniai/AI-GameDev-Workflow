# 📌 폴더 구성
## ☑️ 각 역할별 핵심 체크리스트 파일 내용

**1. PD-Core-Checklist.md**
- 코어 가설 1문장 정의: ⭐️핵심 재미⭐️
- 필수 기능 7개 선정
- 4주 마일스톤 설정
- 리스크 TOP3 관리

**2. Art-Production-Checklist.md**
- 아트 스타일 가이드 10샘플
- ComfyUI 워크플로 구축
- 리소스 일괄 생성 시스템
- 일관성 품질 관리

**3. Development-Checklist.md**
- 모듈 구조 설계 (4개 모듈)
- 수익화 1종 이상 구현
- 분석 SDK 연동
- 릴리즈 빌드 최적화

**4. QA-Testing-Checklist.md**
- 핵심 기능 테스트
- 수익화 기능 검증
- 성능/호환성 확인
- 스토어 제출 준비

---

# 💬 부연설명

## 필수 기능 7개 선정 기준

### 🎯 7개 선정 이유
- **인지 부하 한계**: 인간이 한번에 관리할 수 있는 항목은 7±2개
- **4주 개발 현실성**: 기능당 평균 3-4일 필요 → 7개 × 3일 = 21일 (여유 포함)
- **MVP 원칙**: 최소 기능으로 시장 검증 후 확장
- **1인 개발 한계**: 더 많으면 품질 저하, 출시 지연

### ✅ 적용 방법
1. 기능 후보 리스트 작성 (제한 없음)
2. AI로 우선순위 매트릭스 생성
3. 상위 7개만 MVP에 포함
4. 나머지는 v1.1 업데이트로 보류

---

## 모듈 구조 설계 (4개 핵심 모듈)

### 🏗️ Unity C# 기준 모듈 아키텍처

| 모듈명 | 역할 | 구체적 기능 | 의존성 |
|--------|------|-------------|--------|
| **EventSystem** | 게임 이벤트 처리 | 레벨업, 아이템획득, 미션완료 등 | 없음 (독립) |
| **DialogSystem** | UI 알림/대화 | 팝업, 알림, NPC 대화, 튜토리얼 | EventSystem만 의존 |
| **SaveSystem** | 데이터 저장/로드 | 진행상황, 설정, 구매내역 저장 | 없음 (독립) |
| **MonetizeSystem** | 수익화 기능 | 광고 재생, IAP 구매, 보상 지급 | EventSystem만 의존 |

### 💡 설계 원칙
- **낮은 의존성**: 각 모듈이 독립적으로 동작
- **재사용성**: 다른 프로젝트에 쉽게 이식 가능
- **유지보수성**: 모듈별로 독립적 수정/업데이트

### 📝 예시 인터페이스
```csharp
// EventSystem 예시
public interface IEventSystem
{
    void TriggerEvent(string eventName, object data = null);
    void RegisterListener(string eventName, System.Action<object> callback);
    void UnregisterListener(string eventName, System.Action<object> callback);
}
```

---

## 분석 SDK 연동 가이드

### 🥇 1순위: Firebase Analytics (무료)
```csharp
// 기본 설정
Firebase.FirebaseApp.CheckAndFixDependenciesAsync();

// 필수 이벤트 추적
FirebaseAnalytics.LogEvent("tutorial_complete");
FirebaseAnalytics.LogEvent("level_start", "level_name", "level_1");
FirebaseAnalytics.LogEvent("level_complete", new Parameter[] {
    new Parameter("level_name", "level_1"),
    new Parameter("score", 1500)
});

// 수익 추적
FirebaseAnalytics.LogEvent("ad_reward_earned", new Parameter[] {
    new Parameter("ad_unit_id", "rewarded_video"),
    new Parameter("value", 100),
    new Parameter("currency", "coins")
});

// IAP 추적
FirebaseAnalytics.LogEvent(FirebaseAnalytics.EventPurchase, new Parameter[] {
    new Parameter(FirebaseAnalytics.ParameterTransactionId, "transaction_123"),
    new Parameter(FirebaseAnalytics.ParameterValue, 0.99),
    new Parameter(FirebaseAnalytics.ParameterCurrency, "USD")
});
```

### 🥈 2순위: GameAnalytics (게임 특화)
- **특징**: 게임 특화 분석, 무료 월 10만 이벤트
- **장점**: 세션 길이, 진행률, 이탈 구간 자동 분석
- **설치**: Unity Package Manager에서 GameAnalytics SDK

### 🥉 3순위: Unity Analytics (Unity 통합)
- **특징**: Unity 에디터 통합, 기본 지표 자동 수집
- **장점**: 별도 SDK 불필요, 에디터에서 바로 확인
- **한계**: 커스터마이징 제한적

### 📊 필수 추적 이벤트
- `app_start` - 앱 시작
- `tutorial_complete` - 튜토리얼 완료
- `level_start` / `level_complete` - 레벨 시작/완료
- `ad_watched` - 광고 시청
- `iap_purchase` - 인앱 구매
- `user_retention` - 유저 리텐션

---

## 릴리즈 빌드 최적화 체크리스트

### ⚙️ Unity Player Settings

| 설정 카테고리 | 항목 | 개발용 | 릴리즈용 | 효과 |
|---------------|------|--------|----------|------|
| **Configuration** | Scripting Backend | Mono | IL2CPP | 성능 20% 향상, 보안 강화 |
| **Configuration** | Api Compatibility Level | .NET Framework | .NET Standard 2.1 | 호환성, 크기 최적화 |
| **Configuration** | Target Architectures | x86 | ARM64 | 모든 안드로이드 기기 지원 |
| **Publishing Settings** | Minify | None | Proguard | APK 크기 30% 감소 |
| **Publishing Settings** | Use App Bundle | 체크 해제 | 체크 | Google Play 필수 |

### 🔧 Build Settings
```
Development Build: 체크 해제
Script Debugging: 체크 해제
Deep Profiling: 체크 해제
Compression Method: LZ4 (빠른 압축)
```

### 📦 추가 최적화 설정

#### Managed Stripping Level
- **설정값**: `High`
- **효과**: 사용하지 않는 코드 제거로 APK 크기 50% 감소
- **주의사항**: 리플렉션 사용 코드는 `link.xml`에 명시 필요

#### Texture Compression
- **Android**: ASTC
- **iOS**: ASTC/PVRTC
- **효과**: 텍스처 용량 70% 감소

#### Audio Compression
- **BGM**: Vorbis, Quality 0.5
- **SFX**: ADPCM
- **효과**: 오디오 용량 60% 감소

### 📈 최적화 결과
- **APK 크기**: 30-50% 감소
- **로딩 속도**: 20-30% 향상  
- **런타임 성능**: 10-20% 향상
- **메모리 사용량**: 15-25% 감소

---

## 수익화 기능 검증 체크리스트

### 💰 광고 기능 테스트

#### 로드 테스트
- [ ] **WiFi 환경**: 광고 로드 성공률 95% 이상
- [ ] **모바일 데이터**: 광고 로드 성공률 90% 이상
- [ ] **네트워크 끊김**: 5초 후 재시도 로직 작동
- [ ] **광고 없음**: "광고가 준비되지 않았습니다" 메시지 표시

#### 시청 테스트  
- [ ] **완전 시청**: 30초 광고 끝까지 시청 시 보상 정확 지급
- [ ] **중간 종료**: 사용자가 중간에 닫을 시 보상 미지급
- [ ] **스킵 방지**: 스킵 버튼 비활성화 (보상형 광고)
- [ ] **음소거 처리**: 게임 BGM 일시정지, 광고 종료 후 복원

#### 빈도 제어 테스트
- [ ] **연속 제한**: 동일 광고 3번 연속 노출 방지
- [ ] **시간 제한**: 1시간 내 최대 6회 제한
- [ ] **쿨타임**: 광고 시청 후 30초 쿨타임 적용
- [ ] **강제 광고 방지**: 사용자 의도와 무관한 광고 노출 금지

### 💳 IAP 기능 테스트

#### 구매 프로세스
- [ ] **결제 창 연동**: Google Play/App Store 정상 연결
- [ ] **상품 정보**: 가격, 설명 정확 표시
- [ ] **구매 완료**: 결제 성공 후 아이템 즉시 지급
- [ ] **구매 실패**: 네트워크 오류 시 적절한 에러 메시지

#### 보안 검증
- [ ] **영수증 검증**: 서버사이드 영수증 유효성 확인
- [ ] **중복 구매 방지**: 동일 거래 ID 중복 처리 차단
- [ ] **해킹 방지**: 클라이언트 단독 아이템 지급 금지

#### 복원/환불 테스트
- [ ] **기기 변경**: 새 기기에서 구매 내역 복원
- [ ] **앱 재설치**: 삭제 후 재설치 시 구매 내역 유지
- [ ] **환불 처리**: 환불 시 구매 아이템 자동 회수
- [ ] **가족 공유**: iOS 가족 공유 구매 정상 처리

### 📊 수익화 데이터 추적
```csharp
// 광고 수익 추적
Analytics.LogEvent("ad_impression", new Dictionary<string, object> {
    {"ad_unit", "rewarded_video"},
    {"revenue", 0.02f},
    {"currency", "USD"}
});

// IAP 수익 추적  
Analytics.LogEvent("iap_purchase", new Dictionary<string, object> {
    {"product_id", "remove_ads"},
    {"price", 2.99f},
    {"currency", "USD"},
    {"transaction_id", purchaseToken}
});
```

---

## 성능/호환성 확인 기준

### 📊 성능 측정 기준

| 측정 항목 | 최소 기준 | 권장 기준 | 측정 도구 | 측정 시점 |
|-----------|-----------|-----------|-----------|-----------|
| **메모리 사용량** | <200MB | <150MB | Unity Profiler | 10분 연속 플레이 |
| **FPS** | 30fps 이상 | 60fps | Unity Profiler | 저사양 기기 기준 |
| **배터리 소모** | <5%/10분 | <3%/10분 | 기기별 배터리 설정 | 연속 플레이 시 |
| **로딩 시간** | <5초 | <3초 | 스톱워치 | 앱 시작~메인 화면 |
| **앱 용량** | <100MB | <50MB | Build Report | 최종 APK/IPA |

### 📱 호환성 테스트 기기

#### 필수 테스트 기기 (최소 3종)
- **Android 저사양**: Galaxy A 시리즈, Xiaomi Redmi
- **iOS 저사양**: iPhone SE (2세대)  
- **최신 플래그십**: Galaxy S 시리즈 or iPhone Pro 최신

#### 해상도 호환성 체크
- **16:9** (1920x1080) - 구형 기기
- **18:9** (2340x1080) - 중형 기기  
- **19.5:9** (2436x1125) - iPhone X 이후
- **20:9** (2400x1080) - 최신 안드로이드

#### OS 버전 지원 범위
```
Android:
- 최소: API 21 (Android 5.0) - 98% 기기 지원
- 타겟: API 34 (Android 14) - Google Play 정책

iOS:
- 최소: iOS 12.0 - 95% 기기 지원  
- 타겟: iOS 17.0 - 최신 기능 활용
```

### 🔍 성능 프로파일링 체크리스트

#### 메모리 사용량 분석
- [ ] **Heap Size**: 150MB 이하 유지
- [ ] **Texture Memory**: 전체 메모리의 30% 이하
- [ ] **Audio Memory**: 20MB 이하
- [ ] **Memory Leak**: 5분 플레이 후 메모리 증가 <10MB

#### CPU 성능 분석  
- [ ] **Main Thread**: 16ms 이하 (60fps 기준)
- [ ] **Render Thread**: 16ms 이하
- [ ] **GC Allocations**: 프레임당 1KB 이하
- [ ] **Draw Calls**: 100개 이하 (모바일 기준)

#### GPU 성능 분석
- [ ] **Fill Rate**: 화면 픽셀의 2배 이하
- [ ] **Triangle Count**: 10만개 이하
- [ ] **Texture Bandwidth**: 적정 범위 내
- [ ] **Shader Complexity**: 복잡한 셰이더 최소화

### 🚨 크래시 0 달성 기준

#### 안정성 테스트
- [ ] **10분 연속 플레이**: 크래시 없음
- [ ] **앱 전환 10회**: 백그라운드↔포그라운드 전환 후 정상 동작
- [ ] **메모리 부족 시뮬**: 저사양 기기에서 메모리 경고 시 정상 처리
- [ ] **네트워크 끊김**: 인터넷 연결 없을 때 적절한 에러 처리

#### 예외 상황 처리
- [ ] **권한 거부**: 필수 권한 거부 시 안내 메시지
- [ ] **저장공간 부족**: 세이브 실패 시 사용자 알림
- [ ] **기기 회전**: 세로↔가로 전환 시 UI 정상 표시
- [ ] **전화 수신**: 게임 중 전화 와도 정상 복귀

### 📋 최종 출시 전 체크리스트
- [ ] 모든 성능 지표 기준 충족
- [ ] 3종 기기에서 크래시 0 확인  
- [ ] 해상도별 UI 정상 표시
- [ ] 수익화 기능 완벽 동작
- [ ] 분석 이벤트 정상 송신
- [ ] 개인정보 처리 방침 준수

