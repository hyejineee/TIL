---
title: Android Dev Summit 2019 Extended Seoul 후기
date: 2019-12-21
category: android
tags: [android, kotlin, coroutines, android-studio, security, theme, livedata]
summary: Security 라이브러리(minSdk 23, Tink 매핑), Android Studio 신기능(multi-preview, desugaring, view binding), Theme/Style 차이, LiveData + Coroutines + Flow 세션 정리.
---

## 🔑 Android Security 라이브러리 (권태환)

- **minSdk: 23** (현재 23이 아니면 사용 불가)
- 제공: 쉐어드 프리퍼런스 암호화, 파일 암호화, key alias
- 기반: **Google Tink** (암호화 라이브러리) — Security 라이브러리는 Tink를 매핑한 것

### 사용 방법 (쉐어드, 파일 암호화)

- 키 생성, 마스터키 사용, key alias 사용
- 암호화 키/벨류 방식을 지정해서 사용

> ⚠️ Tink, Security만으로 암호화해서 데이터를 지키는 것은 좀 무리.

### 안드로이드 생체인식 인증 라이브러리 (Biometric Authentication)
- minSdk: 23

## 🤖 New Android Studio (pluu)

- **Project Structure**: 최신 버전으로 갱신 안 되던 부분 해결
- **Critical Path**: build 시간 추적 가능, incremental build speed (configuration)
- **Multi Preview**: 다양한 디바이스 레이아웃 동시 확인 (디바이스/언어/색 보정)
- **Desugaring**: minSdk에 엮이는 문제 해결 — 라이브러리 내부에 누락된 API 포함
  ```
  coreLibraryDesugaringEnabled = true
  ```
- **View Binding**: 기존 data binding과 차이? findViewById와 달리 not nullable
- **Jetpack Compose**: `@Composable`, 하지만 프리 알파버전
- Layout Inspector, Motion Editor
- Emulator multi display
- Gradle plugin: feature 하나로 통합

## Developing Themes with Style (안명욱)

### Theme & Style 차이

| | 키 | 적용 범위 |
| --- | --- | --- |
| **Style** | view 속성을 키로 사용 | 특정 하나의 뷰에만 |
| **Theme** | 테마 속성을 키로 사용 (`?attr/colorPrimary`) | context에 연결, view 계층 구조에 영향 |

- 테마는 여러 테마를 상속받아서 사용 — 제일 나중에 정의된 테마가 중요.
- **테마 오버레이**: 테마의 일부 속성을 변경해서 사용하기 위한 기술.

### Color

- Color State Lists: drawable과 동일한 state 시스템 사용
- API 23에서 테마 컬러 참조 가능

### 네이밍 규칙

```
스타일타입.그룹이름.하위그룹이름.<실제 뷰 클래스 이름>.variant
```

파일 분리:
- **Easy mode**: 파일 목적에 따라 분리
- Material theme = typo + shape + color = look and feel

### Dark Theme

하드코딩된 색상값이 문제가 될 수 있다. → Material Color Tool 사용. 다크 테마에선 elevation을 그림자로 표현할 수 없으므로 **색상값을 변경**해서 표현.

## LiveData with Coroutines and Flow (이우춘)

- `viewModelScope` (ViewModel extension)
- Activity / Fragment lifecycleScope launch
