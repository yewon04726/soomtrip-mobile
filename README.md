## soomtrip
원스토어 링크 : https://m.onestore.co.kr/v2/ko-kr/app/0001009307

# 숨트립 (SoomTrip) Mobile

취향 월드컵으로 나에게 맞는 국내 여행지를 추천받고, 저장·기록·공유할 수 있는 Flutter 여행 앱입니다.

- 출시: [원스토어](https://m.onestore.co.kr/v2/ko-kr/app/0001009307)
- 백엔드: [soomtrip-server](https://github.com/yewon04726/soomtrip-server) (FastAPI)


---

## 주요 기능

| 기능 | 설명 |
| --- | --- |
| 카카오 로그인 | 카카오 access token을 서버에 전달해 SoomTrip JWT를 발급받아 사용 |
| 취향 월드컵 | 바다/자연, 이동수단, 동행, 활동성, 실내외 등 취향을 선택해 점수화 |
| 여행지 추천 | 월드컵 결과를 서버로 보내 추천 여행지와 주변 관광지를 받아 표시 |
| 저장 여행지 | 마음에 드는 추천 결과를 저장하고 목록에서 확인 |
| 지도·경로 | 네이버 지도 기반 여행지 위치 표시와 경로 안내 |
| 친구 | 친구 요청/수락, 친구에게 저장한 여행지 공유 |
| 그림일기 | 여행지에 사진·그림으로 기록 남기기 |
| 스탬프·가챠 | 여행 기록에 따른 보상 요소 |
| 마이페이지 | 프로필 수정, 저장 목록 관리 |

---

## 기술 스택

- **Framework**: Flutter (Dart SDK ^3.5.0)
- **인증**: `kakao_flutter_sdk_user`
- **지도**: `flutter_naver_map`
- **네트워크**: `http`, `http_parser`
- **로컬 저장**: `shared_preferences`
- **환경 변수**: `flutter_dotenv`
- **미디어**: `image_picker`, `flutter_drawing_board`, `gal`
- **UI**: `animated_text_kit`, `bouncy_background`, 커스텀 폰트
- **기타**: `url_launcher`, `flutter_launcher_icons`, `flutter_native_splash`

---

## 앱 흐름

```text
Splash → Intro → Consent → 카카오 로그인
  → 취향 월드컵 → 추천 결과 → 여행지 상세
  → 저장 / 길찾기 / 친구 공유 / 그림일기
```

서버 호출 순서는 다음과 같습니다.

```text
1. POST /auth/kakao          카카오 토큰 → SoomTrip JWT
2. POST /preferences         월드컵 결과 저장
3. POST /recommendations     추천 여행지 + 주변 관광지
4. POST /saved-regions       저장 버튼
5. GET  /saved-regions       저장 목록
```

인증이 필요한 API는 `Authorization: Bearer <SOOMTRIP_JWT>` 헤더를 사용합니다.

---

## 프로젝트 구조

```text
lib/
├─ main.dart                      앱 진입점 (dotenv, Kakao/Naver SDK 초기화)
├─ *_screen.dart                  화면 (splash, intro, consent, worldcup, home, main,
│                                  destination_*, route, friends, my_page, stamp, gacha ...)
├─ *_service.dart                 서버 API 호출 (auth, preference, recommendation,
│                                  saved_region, friend, route, share, profile, diary_entry)
├─ saved_destinations_controller.dart   저장 여행지 상태 관리
└─ 기타 위젯/유틸 (share_region_dialog, relative_time, profile_images ...)

test/                             서비스·화면 단위/위젯 테스트
docs/android-release.md           Android 릴리스 절차
assets/                           이미지, 폰트, 아이콘
```

---

## 실행 방법

### 1. 사전 준비

- Flutter SDK (Dart ^3.5.0)
- 카카오 네이티브 앱 키, 네이버 지도 클라이언트 ID
- 실행 중인 [soomtrip-server](https://github.com/yewon04726/soomtrip-server)

### 2. 환경 변수

프로젝트 루트에 `.env` 파일을 만듭니다. 실제 값은 Git에 올리지 않습니다.

```env
KAKAO_NATIVE_APP_KEY=
```

### 3. 실행

```bash
flutter pub get
flutter run
```

---

## 테스트

```bash
flutter test
```

- 인증 토큰 갱신(`auth_refresh_test`)
- 서비스 계층: 프로필, 경로, 공유
- 경로 API 흐름과 선택 상태 초기화
- 화면 레이아웃·진입 복구·공유 다이얼로그
