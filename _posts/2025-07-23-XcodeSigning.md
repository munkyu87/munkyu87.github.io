---
layout: single
title: "Xcode Signing & Capabilities 설정 가이드"
categories: TechNote
tags: [Xcode, Signing, iOS, 인증서, Provisioning Profile]
toc: true
toc_sticky: true
author_profile: true
---

# 📌 개요

iOS 앱을 빌드해서 **디바이스에 설치하거나 앱스토어에 업로드**하려면 반드시 서명(Signing)이 필요합니다.  
여기서 서명에 필요한 **인증서/프로비저닝 프로파일/팀** 등을 지정하게 됩니다.

아래는 Xcode의 **Signing & Capabilities > Signing** 화면의 각 항목에 대한 상세 설명입니다.

---

# 📌 Signing 설정 항목

---

## Automatically manage signing

- 체크하면 Xcode가 자동으로:
  - 인증서(Certificate)
  - App ID
  - Provisioning Profile
    를 생성하거나 선택해서 맞춰줍니다.
- 초보자나 테스트 용도라면 무조건 켜두세요.
- App Store 배포를 위해 수동으로 세팅해야 하는 경우라면 체크를 해제하고 직접 지정합니다.

---

## Team

- **Apple Developer 계정**을 선택하는 곳입니다.
- 개인 계정, 회사 계정 등 Apple에 등록된 개발자 팀이 모두 나타납니다.
- 예시:
  - testMan (Personal Team): 개인 Apple ID
  - Company: 회사 계정
- 선택한 팀에 따라 사용할 수 있는 인증서/프로비저닝 프로파일이 달라집니다.
- 회사 앱이라면 회사 계정을, 개인 앱이라면 Personal Team을 선택하세요.

---

## Bundle Identifier

- 앱을 유일하게 식별하는 ID입니다.  
  (Android의 패키지명과 유사)
- 형식:com.companyname.appname
- 회사 도메인을 거꾸로 적고 뒤에 앱 이름을 붙이는 것이 관례입니다.
- 예: `org.reactjs.native.example.MyFriendsTests`
- ⚠️ App Store에는 동일한 Bundle ID를 가진 앱이 등록될 수 없습니다. 반드시 고유해야 합니다.

---

## iOS > Signing Certificate

- 앱을 서명하는 데 사용될 **개발 인증서**입니다.
- 자동 관리 시:
- 개발용: `Apple Development: <이메일>`
- 배포용: `Apple Distribution: <이메일>`
- ⚠️ Team에 맞는 인증서를 사용해야 디바이스에서 실행 가능하고, 스토어 업로드가 됩니다.

---

## iOS > Provisioning Profile

- 인증서 + 디바이스 + App ID를 연결하는 파일.
- 자동 관리 시:
- Xcode가 자동으로 생성 & 선택합니다.
- 화면에는 `None Required`로 표기되기도 합니다.
- 수동 관리 시:
- Apple Developer 사이트에서 직접 생성한 `.mobileprovision` 파일을 다운로드해 지정합니다.

---

## macOS

- macOS 앱(테스트 타겟 등)을 빌드할 경우의 서명 설정입니다.
- iOS와 구조는 동일합니다.
- 여기서 지정하지 않아도 iOS 앱 빌드에는 영향 없습니다.

---

# 📌 요약 표

| 항목                             | 설명                                      |
| -------------------------------- | ----------------------------------------- |
| **Automatically manage signing** | Xcode가 인증서 & 프로파일을 자동으로 설정 |
| **Team**                         | Apple Developer 계정(개인 or 회사)        |
| **Bundle Identifier**            | 앱의 고유 ID                              |
| **Signing Certificate**          | 앱을 서명할 개발/배포 인증서              |
| **Provisioning Profile**         | 인증서 + 디바이스를 연결하는 파일         |

---

# 📌 팁

- 개인 개발자(Personal Team)는 무료지만, 디바이스 3대까지만 테스트 가능하며 App Store 업로드는 불가합니다.
- 유료 Apple Developer Program (99$/년)을 가입하면 무제한 디바이스 + App Store 업로드 가능.
- 디바이스에서 “신뢰되지 않은 개발자” 오류가 발생하면:

- 디바이스에서 `설정 → 일반 → 기기 관리`에서 인증서를 **신뢰**로 변경해 주세요.

---

> 🚀 팀과 번들 ID를 제대로 선택하고 자동 서명을 켜두면 테스트까지는 빠르게 진행할 수 있습니다.  
> 배포를 위해서는 회사 계정과 정확한 Bundle ID를 지정해 주어야 합니다.
