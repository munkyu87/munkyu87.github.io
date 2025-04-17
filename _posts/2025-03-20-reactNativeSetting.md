---
layout: single
title: "React Native 환경 설정 및 실행 과정"
categories: SideProject
tags: [React Native, iOS, Android]
---

# React Native 환경 설정 및 실행 과정

이 문서는 **React Native 프로젝트를 처음 시작하는 과정**을 기록한 것입니다.  
**iOS 및 Android 개발 환경 구축, Node.js, Java, CocoaPods 설정 방법**을 포함합니다.  
👉 **Mac 기준**으로 작성되었습니다.

---

## 1. 환경 준비

### 📌 ** 필수 도구 설치**

React Native 개발을 위해 다음과 같은 도구가 필요합니다.

- **Node.js** (v22 이상)
- **npm** (최신 버전)
- **nvm** (Node.js 버전 관리)
- **Java JDK 17** (Android Studio 및 Gradle 지원)
- **CocoaPods** (iOS 네이티브 라이브러리 관리)
- **Xcode** (iOS 개발)
- **Android Studio** (Android 개발)

---

## 2. Node.js & npm 설치

### **Node.js 설치 (nvm 사용)**

1️⃣ `nvm`이 설치되어 있는지 확인합니다.

```sh
command -v nvm
```

2️⃣ nvm이 없다면 아래 명령어로 설치합니다.

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.4/install.sh | bash
source ~/.zshrc   # 또는 source ~/.bashrc
```

3️⃣ 최신 Node.js 버전 설치 및 사용:

```sh
nvm install --lts
nvm use --lts
```

4️⃣ 정상적으로 설치되었는지 확인:

```sh
node -v
npm -v
```

---

## 3. Xcode & CocoaPods 설정 (iOS 환경)

### (1) Xcode 설치 - 설치 생략..

### (2) CocoaPods 설치

```sh
sudo gem install cocoapods
pod setup
```

- 설치확인

```sh
pod --version
```

---

## 4. Android 개발 환경 설정

### (1) Android Studio 설치 - 설치 생략.. - 설치 후 Android SDK & AVD(에뮬레이터) 설정 진행

### (2) Java JDK 설치

```sh
brew install openjdk@17
```

- 설치확인

```sh
java --version
```

### (3) Android 환경 변수 설정

```sh
echo 'export ANDROID_HOME=$HOME/Library/Android/sdk' >> ~/.zshrc
echo 'export PATH=$ANDROID_HOME/emulator:$ANDROID_HOME/tools:$ANDROID_HOME/tools/bin:$ANDROID_HOME/platform-tools:$PATH' >> ~/.zshrc
source ~/.zshrc
```

- 환경변수 적용 확인

```sh
echo $ANDROID_HOME
```

---

<div class="ads-area in-article">
  <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-3629284805087203"
      crossorigin="anonymous"></script>
  <!-- in-article-1 -->
  <ins class="adsbygoogle"
      style="display:block"
      data-ad-client="ca-pub-3629284805087203"
      data-ad-slot="3410814707"
      data-ad-format="auto"
      data-full-width-responsive="true"></ins>
  <script>
      (adsbygoogle = window.adsbygoogle || []).push({});
  </script>
</div>

## 5. React Native 프로젝트 생성

### (1) 프로젝트 생성

```sh
npx @react-native-community/cli init DailyTeller
```

설치 완료 후 프로젝트 폴더 이동

```sh
cd DailyTeller
```

### (2) iOS 실행 준비

1️⃣ iOS 네이티브 라이브러리 설치

```sh
cd ios
pod install
cd ..
```

2️⃣ iOS 앱 실행

```sh
npx react-native run-ios
```

## 6. Android 실행

### (1) Android 에뮬레이터 실행

1️⃣ 사용 가능한 에뮬레이터 목록 확인

```sh
emulator -list-avds
```

2️⃣ 특정 에뮬레이터 실행

```sh
emulator -avd {YOUR_EMULATOR_NAME}
```

### (2) React Native Android 실행

```sh
npx react-native run-android
```

## 7.실행화면

<img src="/assets/images/ReactNativeSetting.png" alt="result" width="100%">
