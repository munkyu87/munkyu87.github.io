---
layout: single
title: "삼성 SmartThings 스마트 플러그 인증 가이드"
categories: IoT
tags: [OAuth2.0, 인증, 스마트싱스, 스마트플러그]
toc: true
toc_sticky: true
author_profile: true
---

# 📌 개요

이 문서는 **삼성 SmartThings 스마트 플러그 API**를 사용하기 위한 **OAuth2.0 인증 절차**를 정리한 가이드입니다. 사용자 인증 방식과 보안 흐름에 대한 개념을 이해하고, 실습을 통해 **Access Token과 Refresh Token을 안전하게 발급·관리하는 방법**을 설명합니다.

- **인증 프로토콜**: OAuth 2.0 Authorization Code Grant
- **플랫폼 유형**: SPA / Web App (React 기반 포함)

---

# 📌 OAuth2.0 인증 방식 개념

OAuth2.0은 **사용자의 자격증명을 직접 노출하지 않고**, 인증된 제3자가 리소스에 접근할 수 있게 해주는 인증/인가 프레임워크입니다.

## ✅ Authorization Code Grant Flow

가장 안전하고 표준적인 방식으로, 다음 절차로 진행됩니다:

| 단계 | 설명                                                                                          |
| ---- | --------------------------------------------------------------------------------------------- |
| 1    | 사용자 로그인 및 동의 요청 (브라우저 통해 SmartThings 로그인)                                 |
| 2    | 일회용 코드(authorization code) 반환                                                          |
| 3    | 서버에서 이 코드를 이용해 access_token & refresh_token 발급 요청                              |
| 4    | access_token으로 스마트 플러그 API 요청                                                       |
| 5    | access_token이 만료되면 refresh_token으로 새 토큰 요청 (이 시점에 기존 access_token은 만료됨) |

---

# 📌 인증 방식 종류 비교

| 구분               | Personal Access Token (PAT) | OAuth2.0 Authorization Code            |
| ------------------ | --------------------------- | -------------------------------------- |
| 대상               | 단일 사용자용               | 다수 사용자 인증/인가 가능             |
| 보안성             | 낮음 (Token 노출 주의)      | 높음 (Code+Secret+Redirect 기반)       |
| 사용 시점          | 빠른 테스트/개발용          | 서비스화/유저 인증 필요한 경우         |
| access_token 만료  | 없음                        | 24시간 (expires_in = 86400초)          |
| refresh_token 만료 | 없음                        | 30일 이내 유효 (명시X, 자동 만료 가능) |

---

# 📌 OAuth 인증 구현 흐름

## ✅ 1단계. OAuth 앱 등록 (CLI or Portal)

```bash
smartthings apps:create
```

### 🔽 프로그램 설치 방법

1. SmartThings CLI 설치:

- mac
  ```bash
  npm install -g @smartthings/cli
  ```
- windows
  [Git CLI 설치](https://github.com/SmartThingsCommunity/smartthings-cli/releases)
  - smartthings.msi 설치

2. 로그인:

   ```bash
   smartthings login
   ```

3. 앱 생성:

   ```bash
   smartthings apps:create
   ```

- Display Name: SmartPlug
- Redirect URI: `https://oauth.pstmn.io/v1/callback` or `http://localhost:3000/callback`
- Scopes: `r:devices:* w:devices:* x:devices:*`

등록 완료 시 아래 정보 확인:

- OAuth Client ID
- OAuth Client Secret

- 예시 화면
  <img src="/assets/images/smartThingsCLI.png" alt="sequenceDiagram" width="100%">{: .align-center}

---

## ✅ 2단계. 인증 요청 (사용자 동의 + Code 반환)

```http
GET https://api.smartthings.com/oauth/authorize?
  client_id=...&
  response_type=code&
  scope=r:devices:*+w:devices:*+x:devices:*&
  redirect_uri=https://oauth.pstmn.io/v1/callback
```

→ 브라우저 주소창에 위 URL 입력 → 로그인 및 동의 후
→ Redirect URI에 다음 형태로 `code` 포함되어 리턴됨:

```
https://oauth.pstmn.io/v1/callback?code=abcXYZ123
```

---

## ✅ 3단계. Access Token 발급

Postman에서 아래 설정으로 `POST` 요청:

- URL: `https://auth-global.api.smartthings.com/oauth/token`
- Authorization: `Basic Auth` (ID = Client ID, PW = Client Secret)
- Body → x-www-form-urlencoded:

```json
{
  "grant_type": "authorization_code",
  "code": "abcXYZ123",
  "redirect_uri": "https://oauth.pstmn.io/v1/callback"
}
```

**응답:**

```json
{
  "access_token": "...",
  "refresh_token": "...",
  "expires_in": 86399
}
```

---

## ✅ 4단계. API 요청 테스트

예: 디바이스 목록 요청

```http
GET https://api.smartthings.com/v1/devices
Authorization: Bearer {access_token}
```

**401 오류가 발생하면 → access_token 만료됨 → refresh_token으로 새로 발급받기 필요**

---

## ✅ 5단계. Access Token 갱신 (Refresh Token 사용)

```http
POST https://api.smartthings.com/oauth/token
```

- Authorization: Basic Auth
- Body:

```json
{
  "grant_type": "refresh_token",
  "refresh_token": "...",
  "redirect_uri": "https://oauth.pstmn.io/v1/callback"
}
```

응답에서 새로운 access_token 및 refresh_token을 받게 됩니다. 이전 access_token은 **즉시 만료 처리되며, 더 이상 사용할 수 없습니다.**

---

# 📋 유의 사항 및 팁

| 항목                  | 내용                                                                      |
| --------------------- | ------------------------------------------------------------------------- |
| access_token 수명     | 기본 24시간 (expires_in: 86400 초)                                        |
| refresh_token 수명    | 약 30일 유효 (정확한 만료일자 제공 X)                                     |
| refresh_token 사용 시 | 사용한 refresh_token은 만료되며, 새 access/refresh 쌍으로 교체됨          |
| redirect_uri          | OAuth 앱 등록 시 지정한 URI와 완전히 동일해야 함                          |
| SPA 환경 적용         | React에서 URL callback 후 `code` 추출 → 백엔드로 전달 또는 토큰 직접 요청 |

---

# 🧪 Postman 설정 팁

- Authorization → Basic Auth: client_id / client_secret 입력
- Body → x-www-form-urlencoded
- Header 자동 세팅: Content-Type: application/x-www-form-urlencoded

---
