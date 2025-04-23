---
layout: single
title: "삼성 SmartThings 스마트 플러그 API 연동 가이드"
categories: IoT
tags: [SmartThings, IoT, 스마트플러그]
toc: true
toc_sticky: true
author_profile: true
---

# 📌 개요

이 글은 **삼성 SmartThings 전용 스마트 플러그**를 활용하여, **RESTful API를 통해 상태 확인 및 전원 제어 기능을 구현**하는 내용을 정리한 가이드입니다.

- **사용 장비**: 삼성 SmartThings 싱스원 Wi-Fi 스마트 플러그 (IA70001-OTP03)
- **API 제공**: SmartThings 공식 API (무료)
- **기반 플랫폼**: 삼성 SmartThings 클라우드
- **기능**: 전원 ON/OFF, 상태 조회, 전력 소비량 확인 (모델별)

---

# 📌 사용 장비

| 항목      | 설명                                                                                         |
| --------- | -------------------------------------------------------------------------------------------- |
| 제품명    | 삼성 SmartThings Wi-Fi 스마트 플러그                                                         |
| 모델명    | IA70001-OTP03                                                                                |
| 제조사    | 포엠아이                                                                                     |
| 공식 링크 | [제품 페이지 바로가기](https://www.samsung.com/sec/smartthings/IA70001-OTP03/IA70001-OTP03/) |
| 플랫폼    | Samsung SmartThings                                                                          |
| 연결 방식 | Wi-Fi (2.4GHz)                                                                               |
| 지원 기능 | 전원 제어, 전력 사용량 측정 (기능 지원 모델 한정)                                            |

---

# 📌 API 문서 및 사용 준비

## ✅ API 문서 링크

<!-- | 설명                       | 링크
- 📘 **SmartThings Public API Docs**: [https://developer.smartthings.com/docs/api/public](https://developer.smartthings.com/docs/api/public)
- 📘 **Device Capabilities**: [https://developer.smartthings.com/docs/devices/capabilities](https://developer.smartthings.com/docs/devices/capabilities)                                                                            | -->

| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| 📘 SmartThings Public API Docs | [https://developer.smartthings.com/docs/api/public](https://developer.smartthings.com/docs/api/public)
| 📘 Device Capabilities | [https://developer.smartthings.com/docs/devices/capabilities](https://developer.smartthings.com/docs/devices/capabilities)
| 📘 디바이스 목록 조회 | [GET /devices](https://developer.smartthings.com/docs/api/public#tag/Devices) |
| 📘 디바이스 상세 조회 | [GET /devices/{deviceId}](https://developer.smartthings.com/docs/api/public#tag/Devices/operation/getDevice) |
| 📘 디바이스 상태 조회 | [GET /devices/{deviceId}/status](https://developer.smartthings.com/docs/api/public#tag/Devices/operation/getDeviceStatus) |
| 📘 디바이스 제어 명령 | [POST /devices/{deviceId}/commands](https://developer.smartthings.com/docs/api/public#tag/Devices/operation/executeDeviceCommands) |
| 📘 기능 목록(capabilities) | [GET /capabilities](https://developer.smartthings.com/docs/api/public#tag/Capabilities) |

## ✅ 연동 전 준비 사항

1. 삼성 계정 생성 및 SmartThings 앱 설치
2. 스마트 플러그 제품 등록 (같은 계정으로 A/B집 모두 가능)
3. SmartThings 개발자 등록 → [https://developer.smartthings.com](https://developer.smartthings.com)
4. Personal Access Token 발급 (권한: devices:read, devices:control)
5. Device ID 확인 (`GET /devices` API 사용)

---

# 📌 API 기능 목록

| Capability    | 기능 설명                   | 사용 여부         |
| ------------- | --------------------------- | ----------------- |
| `switch`      | 전원 ON/OFF 제어            | ✅                |
| `powerMeter`  | 실시간 전력 소비량 조회 (W) | ✅ (모델 지원 시) |
| `energyMeter` | 누적 에너지 사용량 (kWh)    | ✅ (일부 모델)    |

---

# 📌 Token 정보

| 종류            | 설명                                                                        |
| --------------- | --------------------------------------------------------------------------- |
| `Access Token`  | ⏰ 24시간 후 만료<br>`expires_in: 86400` (응답 JSON에 명시됨)               |
| `Refresh Token` | ⏳ **시간 기반 만료 없음**<br>단, 다음 상황에서만 무효화됨:                 |
| ▸               | 🔸 사용자가 SmartThings에서 서비스 직접 제거 (예: 연결된 기기 모두 삭제 등) |
| ▸               | 🔸 세션 로그아웃 또는 클라우드 접근 중단                                    |

> [공식 문서](https://developer.smartthings.com/docs/getting-started/authorization-and-permissions?utm_source=chatgpt.com)  
> [공식 커뮤니티 답변](https://community.smartthings.com/t/st-schema-does-refresh-token-also-have-expire-time/254136)

---

# 📌 주요 API 호출 예시

### 1. 디바이스 목록 조회

```http
GET /v1/devices
Authorization: Bearer {token}
```

**Response 예시:**

```json
{
  "items": [
    {
      "deviceId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "싱스원 플러그",
      "label": "거실 플러그",
      "locationId": "...",
      "roomId": "..."
    }
  ]
}
```

### 2. 디바이스 상태 확인

```http
GET /v1/devices/{deviceId}/status
Authorization: Bearer {token}
```

**Response 예시:**

```json
{
  "components": {
    "main": {
      "switch": {
        "switch": {
          "value": "on"
        }
      },
      "powerMeter": {
        "power": {
          "value": 3.5,
          "unit": "W"
        }
      }
    }
  }
}
```

### 3. 전원 ON/OFF 명령 전송

```http
POST /v1/devices/{deviceId}/commands
Authorization: Bearer {token}
Content-Type: application/json

Body:
{
  "commands": [
    {
      "component": "main",
      "capability": "switch",
      "command": "off" // 또는 "on"
    }
  ]
}
```

**Response 예시:**

```json
{
  "results": [
    {
      "id": "main",
      "status": "ACCEPTED"
    }
  ]
}
```

---

# 📬 Postman 사용 팁

1. Authorization 헤더에 `Bearer {ACCESS_TOKEN}` 추가
2. `POST /devices/{deviceId}/commands` 사용 시 Body에 JSON 입력:

```json
{
  "commands": [
    {
      "component": "main",
      "capability": "switch",
      "command": "on"
    }
  ]
}
```

3. 응답 코드가 `200 OK` 또는 `ACCEPTED`이면 정상 작동

---

# 📌 요금제 및 라이선스

| 항목        | 내용                                  |
| ----------- | ------------------------------------- |
| API 요금제  | ✅ **무료**                           |
| 호출 제한   | 없음 (비정상 abuse 시 차단될 수 있음) |
| 상업적 사용 | 가능 (별도 약관에 따라 사용 가능)     |

---

# 📌 SmartThings 연동 흐름

1. 사용자 계정으로 스마트 플러그 등록
2. 개발자 포털에서 Access Token 발급
3. 디바이스 목록 API로 Device ID 확인
4. 원하는 Device ID로 상태 조회 및 명령 전송
5. 다양한 자동화, 통계, 모니터링 기능 확장 가능
