---
layout: single
title: “ADB 명령어를 활용한 APK 설치 가이드”
categories: Frontend
tags: [APK, Android, ADB]
toc: true
toc_sticky: true
author_profile: true
---

# 📌 개요

React Native 프로젝트에서 APK 파일을 안드로이드 실기기에 수동 설치할 때 사용하는 adb 명령어를 정리한 가이드입니다.
Android Studio 없이도 APK 설치, 제거, 기기 확인 등의 작업을 CLI에서 수행할 수 있습니다.

---

# 📌 ADB란?

ADB(Android Debug Bridge)는 안드로이드 디바이스와 PC 간 통신을 도와주는 도구입니다.

설치된 Android SDK에 포함되어 있으며, adb 명령어로 디바이스에 APK 설치, 로그 확인, 앱 실행 등을 할 수 있습니다.

---

# 📌 ADB 주요 명령어 모음

### 1️⃣ ADB 서버 재시작 (문제 해결용)

```bash
adb kill-server
adb start-server
```

### 2️⃣ 연결된 디바이스 확인

```bash
adb devices

# 출력 예시:
List of devices attached
R5CX34S4BLY	device
emulator-5554	unauthorized
```

- unauthorized → 폰에서 USB 디버깅 허용 안한 상태
- device → 정상 연결된 상태

### 3️⃣ APK 설치

```bash
adb -s [device_id] install [apk_path]

# 예시:
adb -s R5CX34S4BLY install build-1234567890.apk

# 오류 예시
Failure [INSTALL_FAILED_UPDATE_INCOMPATIBLE]: Existing package signatures do not match newer version

→ 기존 설치된 앱의 signature와 다르기 때문 → 기존 앱 제거 후 재설치
```

### 4️⃣ APK 제거 (앱 삭제)

```bash
adb -s [device_id] uninstall [package_name]

# 예시:

adb -s R5CX34S4BLY uninstall com.jinsit.sallime
```

### 5️⃣ APK 재설치 루틴

```bash
adb -s [device_id] uninstall com.yourapp.package
adb -s [device_id] install your_app.apk
```

---

# 📌 패키지명 확인 방법

### APK의 패키지명을 확인하고 싶을 경우:

- aapt dump badging your_app.apk | grep package

또는

- adb shell pm list packages | grep yourapp

---

# 📌 자주 발생하는 이슈

### 이슈 메시지	원인 및 해결 방법
- unauthorized	디바이스에서 USB 디버깅 허용 필요 (팝업 허용 클릭)
- INSTALL_FAILED_UPDATE_INCOMPATIBLE	패키지 서명이 달라서 설치 불가 → 기존 앱 제거 후 재설치
- 디바이스가 안 보임	USB 케이블 변경, USB 모드 확인, ADB 재시작
- 앱이 2개 설치됨	패키지명이 다른 경우 (예: release vs debug)
- 앱 아이콘 우측 디버그 표시	debuggable=true 설정된 빌드 (디버그 빌드)

---
