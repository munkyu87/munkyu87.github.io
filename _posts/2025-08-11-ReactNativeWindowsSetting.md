---
layout: single
title: "React Native Windows 환경 설정 및 실행 과정"
categories: [Frontend]
tags: [React Native, Windows, UWP]
---

# React Native Windows 환경 설정 및 실행 과정

## 1. 환경 준비

### 📌 **필수 도구 설치**

React Native Windows 개발을 위해 다음 도구가 필요합니다.

- **Node.js** (LTS 권장, v18 이상)
- **npm** (최신 버전)
- **Visual Studio 2022 (17.11 이상)**  
  - C++를 사용한 데스크톱 개발  
  - Windows 애플리케이션 개발  
  - Windows 10 SDK (10.0.19041.0)  
  - C++/WinRT  
  - Windows 유니버설 CRT SDK
- **Windows 개발자 모드 활성화**
- **충분한 디스크 공간** (최소 15GB 이상)

---

## 2. Node.js & npm 설치

1️. Node.js LTS 버전 설치:  
[Node.js 다운로드](https://nodejs.org/en/)

2️. 설치 확인:

```powershell
node -v
npm -v
```

---

## 3. Visual Studio 2022 설치 및 설정
1. Visual Studio Installer 실행
2. [수정] 클릭 후 아래 항목 체크

✅ 워크로드 탭
- C++를 사용한 데스크톱 개발
- Windows 애플리케이션 개발

✅ 개별 구성 요소 탭
- Windows 10 SDK (10.0.19041.0)
- MSVC v143 - VS 2022 C++ x64/x86 빌드 도구
- C++/WinRT
- Windows 유니버설 CRT SDK
- (권장) v143 빌드 도구용 C++/CLI 지원

---

## 4. Windows 개발자 모드 활성화
1. Windows 설정 → 개인정보 및 보안 → 개발자용 이동
2. 개발자 모드 활성화

또는 PowerShell로 실행:

```powershell
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\AppModelUnlock" /t REG_DWORD /f /v "AllowDevelopmentWithoutDevLicense" /d "1"
```

---

## 5. React Native 프로젝트 생성
```powershell
npx @react-native-community/cli init MyWindowsApp --version 0.75.2
cd MyWindowsApp
```

---

## 6. Windows 플랫폼 추가
```powershell
npx @react-native-community/cli init MyWindowsApp --version 0.75.2
cd MyWindowsApp
```

---

## 6. Windows 플랫폼 추가
```powershell
npx @react-native-community/cli init MyWindowsApp --version 0.75.2
cd MyWindowsApp
```

---

## 7. 필수 의존성 확인
PowerShell 관리자 권한으로 실행:
```powershell
.\node_modules\react-native-windows\scripts\rnw-dependencies.ps1
```
- 모든 항목이 OK여야 함

---

## 8. Windows 앱 실행
```powershell
npx react-native run-windows
```
- 모든 항목이 OK여야 함

# 📌 참고
- 공식 문서: React Native Windows
- Windows 10 SDK 다운로드: SDK Archive
- rnw-dependencies.ps1 스크립트로 필수 구성 요소 자동 확인 가능