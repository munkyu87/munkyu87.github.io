---
layout: single
title: "React Native iOS 빌드 가이드"
categories: Frontend
tags: [ReactNative, iOS, EAS Build]
toc: true
toc_sticky: true
author_profile: true
---

# 📌 개요

React Native 앱을 iOS 기기에 설치할 수 있도록 빌드하는 방법을 정리합니다.  
여기서는 두 가지 빌드 방법을 다룹니다.

✅ **Xcode 로컬 빌드 (cd ios)**  
✅ **EAS(Build) 빌드**

---

# 📌 환경

| 항목             | 내용                                 |
| ---------------- | ------------------------------------ |
| OS               | macOS (필수)                         |
| Xcode            | 설치 필요 (최신 버전 권장)           |
| Node.js          | 설치 필요                            |
| React Native CLI | 설치 (`npx` 사용 가능)               |
| EAS CLI          | 설치 (`npm install -g eas-cli`)      |
| Apple Developer  | 유료 계정 필요 (앱 스토어 업로드 시) |

---

# 📌 로컬(Xcode) 빌드

React Native 프로젝트를 직접 Xcode로 빌드하거나, 터미널에서 시뮬레이터/디바이스에 앱을 설치할 수 있습니다.

---

## 1️⃣ 개발용 실행 (시뮬레이터)

```bash
npm run ios
or
npx react-native run-ios
```

---

## 2️⃣ Xcode로 빌드

1. Xcode 실행 → Open → 프로젝트의 ios/MyApp.xcworkspace 선택
2. 상단의 타겟 기기를 선택 (시뮬레이터 또는 물리 디바이스)
3. 메뉴 → Product → Build 또는 ▶️ (Run) 버튼 클릭
4. 빌드가 완료되면 앱이 시뮬레이터 또는 디바이스에 설치됩니다.

---

## 3️⃣ Archive (스토어 업로드용)

### 스토어에 업로드하려면 .ipa 파일을 만들기 위해 Archive를 진행해야 합니다.

1. Xcode에서 상단 Scheme을 Any iOS Device 또는 연결된 디바이스로 변경
2. 메뉴 → Product → Archive 클릭
3. 빌드가 완료되면 Organizer 창이 열리며, 여기서:
   • .ipa 파일로 Export
   • 또는 App Store Connect에 업로드

# 📌 EAS(Build) 빌드

EAS(Build)는 Expo에서 제공하는 클라우드 빌드 서비스입니다.
로컬 환경을 구성하지 않고도 쉽게 ipa 파일을 빌드할 수 있습니다.

---

## 1️⃣ EAS CLI 설치

```bash
npm install -g eas-cli
```

## 2️⃣ EAS 프로젝트 초기화

```bash
eas build:configure
```

## 3️⃣ 빌드

### production 빌드

```bash
eas build -p ios --profile production
```

### development 빌드 (Development Client)

```bash
eas build -p ios --profile development
```

- profile은 eas.json에 정의된 설정을 사용합니다.
  기본으로 production, preview, development가 있습니다.
  로컬 빌드가 필요하다면 --local 옵션 추가.

### eas.json 예시

EAS 빌드 시 어떤 프로필로 어떤 옵션을 쓸지 지정하는 파일입니다.
루트에 eas.json을 만들고 아래와 같이 작성합니다.

```json
{
  "build": {
    "apk": {
      "android": {
        "buildType": "apk"
      },
      "distribution": "internal",
      "channel": "apk"
    },
    "development": {
      "developmentClient": true,
      "distribution": "internal",
      "channel": "development"
    },
    "preview": {
      "distribution": "internal",
      "channel": "preview"
    },
    "production": {
      "distribution": "store",
      "channel": "production"
    }
  }
}
```

#### 프로필 설명

- apk: .apk 빌드
- development: 개발 클라이언트 빌드
- preview: 테스트 빌드, 내부 공유용
- production: 스토어에 배포할 최종 빌드

### app.json 또는 app.config.js 예시

앱의 이름, 아이콘, 버전, 빌드 넘버 등을 정의합니다.
EAS 빌드 시에도 이 파일을 읽습니다.

```json
{
  "expo": {
    "name": "MyApp",
    "slug": "myapp",
    "version": "1.0.0",
    "owner": "testman",
    "extra": {
      "eas": {
        "projectId": "ididid"
      }
    },
    "updates": {
      "enabled": true,
      "checkAutomatically": "ON_LOAD",
      "fallbackToCacheTimeout": 0,
      "url": "https://u.expo.dev/aaaaaaaa"
    },
    "runtimeVersion": "1.0.0",
    "jsEngine": "hermes",
    "android": {},
    "ios": {}
  }
}
```

---

# 📌 OTA (JS 코드 업데이트만)

EAS Update를 사용하면 JS 코드만 배포할 수 있습니다.

---

## production 채널 배포

```bash
eas update --branch production --message "Production OTA 업데이트"
```

## 다른 브랜치에 배포 예시

```bash
eas update --branch preview --message "Preview OTA 업데이트"
```
