---
layout: single
title: "React Native 안드로이드 빌드 가이드"
categories: Frontend
tags: [ReactNative, Android, EAS Build]
toc: true
toc_sticky: true
author_profile: true
---

# 📌 개요

React Native 앱을 안드로이드 기기에 설치할 수 있도록 빌드하는 방법을 정리합니다.  
여기서는 두 가지 빌드 방법을 다룹니다.

✅ **Android Studio 로컬 빌드 (cd android)**  
✅ **EAS(Build) 빌드**

---

# 📌 환경

| 항목             | 내용                            |
| ---------------- | ------------------------------- |
| OS               | macOS / Windows / Linux         |
| Android Studio   | 설치 필요                       |
| Node.js          | 설치 필요                       |
| React Native CLI | 설치 (`npx` 사용 가능)          |
| EAS CLI          | 설치 (`npm install -g eas-cli`) |

---

# 📌 로컬(Android Studio) 빌드

React Native 프로젝트를 직접 Android Studio로 빌드하거나, 터미널에서 APK/AAB 파일을 생성할 수 있습니다.

---

## 1️⃣ 개발용 실행 (에뮬레이터)

```bash
npm run android
or
npx react-native run-android
```

---

## 2️⃣ Android Studio로 빌드

1. Android Studio 실행 → `Open` → 프로젝트의 `android/` 폴더 선택
2. 메뉴 → `Build` → `Build Bundle(s) / APK(s)`
   - APK 빌드: `Build APK(s)`
   - AAB 빌드: `Build Bundle`

- 빌드가 완료되면 android/app/build/outputs/apk/ 또는 android/app/build/outputs/bundle/에 파일이 생성됩니다.

---

## 3️⃣ 터미널에서 APK/AAB 빌드

### APK 빌드

```bash
cd android
./gradlew assembleRelease
```

- 생성된 APK 파일:

  android/app/build/outputs/apk/release/app-release.apk

### AAB 빌드

```bash
cd android
./gradlew bundleRelease
```

- 생성된 AAB 파일:

  android/app/build/outputs/bundle/release/app-release.aab

---

# 📌 EAS(Build) 빌드

EAS(Build)는 Expo에서 제공하는 클라우드 빌드 서비스입니다.
로컬 환경을 구성하지 않고도 쉽게 APK/AAB 파일을 빌드할 수 있습니다.

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

### AAB 빌드

```bash
eas build -p android --profile production
```

### APK 빌드

```bash
eas build -p android --profile preview
```

- profile은 eas.json에 정의된 설정을 사용합니다.
  기본으로 production, preview, development가 있습니다.
  로컬 빌드가 필요하다면 --local 옵션 추가.

### eas.json

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

### app.json 또는 app.config.js

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
