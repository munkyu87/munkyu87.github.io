---
layout: single
title: "밸런스게임 앱 기획서"
categories: SideProject
tags: [ReactNative, SpringBoot, GPT, 익명앱]
toc: true
toc_sticky: true
author_profile: true
---

# 📌 개요

사용자에게 도덕적, 심리적으로 곤란한 질문을 익명으로 던지고,  
투표와 댓글, 질문 평가, 카테고리별 정렬 등을 통해  
재미와 고민, 통계적 공감을 동시에 유도하는 **익명 선택형 커뮤니티 앱**입니다.  
사용자는 질문을 직접 제안할 수도 있고, 어드민 승인 후 실제 질문으로 채택됩니다.

---

# 📌 핵심 기능 요약

| 기능 | 설명 |
|------|------|
| 곤란한 선택 참여 | 익명으로 질문에 투표하고 결과 확인 |
| 질문 결과 화면 | 전체 투표 통계 + 댓글 목록 + 그래프 |
| 질문 별점 평가 | 질문 자체를 0.5 ~ 5.0점 별점으로 평가 |
| 질문 카테고리 | 연애, 사회, 우정, 19금 등으로 분류 |
| 질문 제안 기능 | 사용자가 직접 질문 + 선택지 작성하여 제안 |
| 어드민 검수 시스템 | 어드민이 앱 내에서 제안된 질문 승인/거절 가능 |
| 질문 정렬 기능 | 별점 높은 순, 밸런스 높은 순, 최신순 등 |
| 로그인 | 카카오, 네이버, 구글 연동 로그인 |
| 19금 접근 제어 | 19금 카테고리는 성인 인증한 사용자만 접근 가능 |
| AI 질문 생성 (선택) | GPT API를 통해 주기적으로 자동 질문 생성 |

---

# 📌 화면 구성

## 1️⃣ 로그인 화면
- 소셜 로그인 (카카오, 네이버, 구글)

## 2️⃣ 질문 목록 화면
- 질문 리스트 (카테고리 / 정렬 탭)
- 별점, 카테고리, 참여자 수 등 표시
- 참여한 질문인지 확인 가능

## 3️⃣ 질문 선택 화면
- 질문 본문 + 2~4개의 선택지
- 선택 시 서버에 기록 및 결과 화면 이동

## 4️⃣ 질문 결과 화면
- 선택 비율 원형 그래프 or 막대 그래프
- 유저 코멘트 리스트 및 코멘트 좋아요
- 별점 평가

## 5️⃣ 질문 제안 화면
- 질문 입력 + 선택지(2~4개) 입력
- 선택지 사진은 Optional 사진 등록시 선택지에 등록한 사진이 배경으로 표현
- 카테고리 선택
- 전송 시 “대기 중” 상태로 제출됨

## 6️⃣ 내가 제안한 질문 관리
- 상태: 승인 / 대기 / 거절 목록 구분
- 승인된 질문은 하이라이트 표시
- 거절 사유도 표시 가능

## 7️⃣ 마이페이지
- 내가 참여한 질문/작성한 코멘트 조회가능
- 질문 제안 관리
- 로그아웃 등 설정

## 8️⃣ 어드민 승인 화면 (앱 내부 전용)
- 대기 중 질문 리스트
- 질문/선택지 확인 + [승인], [거절] 버튼
- 거절 시 사유 입력 가능

---

# 📌 기술 스택

| 영역 | 기술 |
|------|------|
| 프론트엔드 | React Native (Cli) |
| 백엔드 | Java Spring Boot |
| 데이터베이스 | MySQL (AWS RDS)|
| 인증 | 소셜 로그인 (카카오, 네이버, 구글) |
| AI 질문 생성 | OpenAI GPT-4 API (선택적 사용) |
| 배포 인프라 | AWS |

---

# 📌 AI 질문 생성 프롬프트

```text
사용자의 심리를 자극하고, 곤란하고 찐한 상황을 던지는 TMI 스타일 질문을 1개 생성해줘.  
선택지는 반드시 2~4개로 제시하고, 누구나 쉽게 고르지만 찝찝한 고민이 생기게끔 해줘.  
유머나 짜증, 자괴감, 도덕 딜레마, 감정 복수심 등이 살짝 들어간 걸로.  
중복되지 않게 전에 썼던 질문은 배제하고.

예시:
- '1억을 받고 가장 싫어하는 사람에게 50억을 주기 vs 아무것도 받지 않기'
- '몰래카메라를 당하는 대신, 친구가 창피당하게 하기 vs 내가 당하기'
```

- ※ AI 질문 생성 주기 (매일, 주 1회, 수동 등)는 관리자 설정에 따라 조정 가능

---

# 📌 정렬 기준 예시

| 기준 | 설명 |
|------|------|
| 별점 높은 순 | 평균 별점 기준 내림차순 정렬 |
| 참여자 많은 순 | 전체 응답자 수 기준 |
| 밸런스 좋은 순 | 선택 비율이 50:50에 가까운 질문|
| 최신 순 | 등록일 기준 최신 질문 우선 |

---

# 📌 19금 카테고리 접근 제어

- 카테고리 중 “19금” 선택 시 성인 인증 여부 확인
- 성인 인증된 사용자만 해당 질문 참여/열람 가능
- 성인 인증 방식: 선택에 따라 추후 연동(API or 수동 체크)

---

# 📌 사용자 흐름 요약

```plaintext
[로그인] → [홈] → [질문 목록 보기] → [질문 선택 참여] → [결과/그래프/댓글]  
          ↓                                          ↓  
          ↓                                      [질문 평가 (별점)]  
          →      [질문 제안] → [어드민 승인] → [실제 질문으로 게시]  
          →      [마이페이지] → [내가 제안한 질문 확인]
                           → [내가 참여한 질문/작성한 코멘트 조회]
                           → [로그아웃]
```

---

# 📌 관리자 흐름 요약

```plaintext
[마이페이지 > 어드민 질문 승인 탭]  
→ 대기 중 질문 목록 확인  
→ 질문 내용/선택지 확인  
→ 승인 or 거절 (사유 입력 가능)  
→ 상태별 통계/정렬 기능도 가능
```

---

# 📌 와이어 프레임 예시 이미지 (추후 바뀔 수 있음!)

## 1️⃣ 질문 목록

```text
+--------------------------------------------------+
| [검색]   [정렬: 최신순 ▼]   [카테고리]                 |
+--------------------------------------------------+
| 💬 "회사 전원이 내 일기장을 본다면?"        2025.08.01  |
| [참여자: 341명]  ⭐ 4.3   🏷️ 연애                   |
| [투표하러가기]                                      |
+--------------------------------------------------+
| 💬 "친구가 몰래 내 흑역사를 캡쳐해놨다"      2025.07.01   |
| [참여자: 912명]  ⭐ 4.7   🏷️ 우정                    |
| [투표하러가기]                                       |
+--------------------------------------------------+
```

<div class="ads-area in-article">
  <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-3629284805087203"
      crossorigin="anonymous"></script>
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
---

# 📌 DB설계 

## 테이블 구성 요약 

| 도메인 | 테이블 |
|--------|--------|
| 사용자 | `users` |
| 질문 | `questions`, `question_choices`, `question_ratings`, `question_categories`, `question_category_map` |
| 투표 응답 | `user_answers` |
| 댓글 | `comments`, `comment_likes` |
| 질문 제안/관리 | `suggested_questions`, `question_review_logs` |

- ### 사용자 - `users`

| 필드명          | 키      | NOT NULL | 데이터 타입                           | 기본값                | 설명                |
| ------------ | ------ | -------- | -------------------------------- | ------------------ | ----------------- |
| id           | PK     | ✅        | BIGINT AUTO\_INCREMENT           | -                  | 사용자 고유 ID         |
| provider     | -      | ✅       | ENUM('kakao', 'naver', 'google') | -                  | 소셜 로그인 제공자        |
| provider_id | UNIQUE | ✅      | VARCHAR(255)                     | -                  | 소셜 사용자 고유 식별값     |
| nickname     | -      | ❌        | VARCHAR(50)                      | NULL               | 사용자 표시 이름 (익명 가능) |
| is_adult    | -      | ❌      | BOOLEAN                          | FALSE              | 성인 인증 여부          |
| is_admin    | -      | ❌       | BOOLEAN                          | FALSE              | 관리자 여부            |
| created_at  | -      | ✅       | DATETIME                         | CT | 가입일               |

- ### 질문 - `questions`

| 필드명           | 키  | NOT NULL | 데이터 타입                       | 기본값                | 설명             |
| ------------- | -- | -------- | ---------------------------- | ------------------ | -------------- |
| id            | PK | ✅        | BIGINT AUTO\_INCREMENT       | -                  | 질문 고유 ID       |
| title       | -  | ✅        | TEXT                         | -                  | 질문 내용          |
| source        | -  | ✅        | ENUM('gpt', 'user')          | -                  | 생성 주체 (AI/사용자) |
| suggested\_by | FK | ❌        | BIGINT                       | NULL               | 제안한 사용자 ID     |
| status        | -  | ❌        | ENUM('published','archived') | 'published'        | 질문 상태          |
| published\_at | -  | ❌        | DATETIME                     | NULL               | 공개 시각          |
| created\_at   | -  | ✅        | DATETIME                     | CT | 생성일            |

- ### 질문 선택 - `question_choices`

| 필드명           | 키  | NOT NULL | 데이터 타입                 | 기본값  | 설명                |
| ------------- | -- | -------- | ---------------------- | ---- | ----------------- |
| id            | PK | ✅        | BIGINT AUTO\_INCREMENT | -    | 선택지 ID            |
| question\_id  | FK | ✅        | BIGINT                 | -    | 연결된 질문 ID         |
| choice\_index | -  | ✅        | INT                    | -    | 선택지 번호 (0부터 시작)   |
| content       | -  | ✅        | VARCHAR(255)           | -    | 선택지 내용            |
| image\_url    | -  | ❌        | TEXT                   | NULL | 이미지 URL(optional) |

- ### 질문 평가 - `question_ratings`

| 필드명          | 키  | NOT NULL | 데이터 타입       | 기본값                | 설명            |
| ------------ | -- | -------- | ------------ | ------------------ | ------------- |
| id           | PK | ✅        | BIGINT       | -                  | 별점 ID         |
| question\_id | FK | ✅        | BIGINT       | -                  | 질문 ID         |
| user\_id     | FK | ✅        | BIGINT       | -                  | 평가한 사용자 ID    |
| rating       | -  | ✅        | DECIMAL(2,1) | -                  | 0.5 \~ 5.0 점수 |
| created\_at  | -  | ✅        | DATETIME     | CT | 생성일시          |

- ### 질문 카테고리 - `question_categories`

| 필드명             | 키  | NOT NULL | 데이터 타입      | 기본값   | 설명      |
| --------------- | -- | -------- | ----------- | ----- | ------- |
| id              | PK | ✅        | BIGINT      | -     | 카테고리 ID |
| name            | -  | ✅        | VARCHAR(50) | -     | 카테고리명   |
| requires\_adult | -  | ❌        | BOOLEAN     | FALSE | 19금 여부  |

- ### 질문 카테고리 매핑 `question_category_map`

| 필드명          | 키  | NOT NULL | 데이터 타입 | 기본값 | 설명      |
| ------------ | -- | -------- | ------ | --- | ------- |
| question\_id | FK | ✅        | BIGINT | -   | 질문 ID   |
| category\_id | FK | ✅        | BIGINT | -   | 카테고리 ID |

> 복합 PK: `(question_id, category_id)`

---

- ### 투표 응답 - `user_answers`

| 필드명           | 키  | NOT NULL | 데이터 타입   | 기본값                | 설명         |
| ------------- | -- | -------- | -------- | ------------------ | ---------- |
| id            | PK | ✅        | BIGINT   | -                  | 응답 ID      |
| user\_id      | FK | ✅        | BIGINT   | -                  | 사용자 ID     |
| question\_id  | FK | ✅        | BIGINT   | -                  | 질문 ID      |
| choice\_index | -  | ✅        | INT      | -                  | 선택한 선택지 번호 |
| created\_at   | -  | ✅        | DATETIME | CT | 응답 일시      |

---

- ### 댓글 - `comments`

| 필드명           | 키  | NOT NULL | 데이터 타입   | 기본값                | 설명              |
| ------------- | -- | -------- | -------- | ------------------ | --------------- |
| id            | PK | ✅        | BIGINT   | -                  | 댓글 ID           |
| question\_id  | FK | ✅        | BIGINT   | -                  | 질문 ID           |
| user\_id      | FK | ✅        | BIGINT   | -                  | 작성자 ID          |
| choice\_index | -  | ❌        | INT      | NULL               | 선택지 기준 댓글 (선택형) |
| content       | -  | ✅        | TEXT     | -                  | 댓글 내용           |
| created\_at   | -  | ✅        | DATETIME | CT | 작성 일시           |

- ### 댓글 좋아요 - `comment_likes`

| 필드명         | 키  | NOT NULL | 데이터 타입   | 기본값                | 설명         |
| ----------- | -- | -------- | -------- | ------------------ | ---------- |
| id          | PK | ✅        | BIGINT   | -                  | 좋아요 ID     |
| comment\_id | FK | ✅        | BIGINT   | -                  | 댓글 ID      |
| user\_id    | FK | ✅        | BIGINT   | -                  | 좋아요 누른 사용자 |
| created\_at | -  | ✅        | DATETIME | CT | 생성 일시      |

---

- ### 질문 제안/관리 - `suggested_questions`

| 필드명            | 키  | NOT NULL | 데이터 타입                                | 기본값                | 설명            |
| -------------- | -- | -------- | ------------------------------------- | ------------------ | ------------- |
| id             | PK | ✅        | BIGINT AUTO\_INCREMENT                | -                  | 제안 질문 ID      |
| user\_id       | FK | ✅        | BIGINT                                | -                  | 제안한 사용자 ID    |
| content        | -  | ✅        | TEXT                                  | -                  | 제안 질문 내용      |
| status         | -  | ✅        | ENUM('pending','approved','rejected') | 'pending'          | 상태 (대기/승인/거절) |
| review\_reason | -  | ❌        | TEXT                                  | NULL               | 거절 사유         |
| created\_at    | -  | ✅        | DATETIME                              | CT | 제안 일시         |
| reviewed\_at   | -  | ❌        | DATETIME                              | NULL               | 처리 일시         |
| reviewed\_by   | FK | ❌        | BIGINT                                | NULL               | 처리한 관리자 ID    |

- ### 질문 제안 승인 로그 `question_review_logs`

| 필드명          | 키  | NOT NULL | 데이터 타입                      | 기본값                | 설명         |
| ------------ | -- | -------- | --------------------------- | ------------------ | ---------- |
| id           | PK | ✅        | BIGINT AUTO\_INCREMENT      | -                  | 로그 ID      |
| question\_id | FK | ✅        | BIGINT                      | -                  | 질문 ID      |
| admin\_id    | FK | ✅        | BIGINT                      | -                  | 관리자 ID     |
| action       | -  | ✅        | ENUM('approved','rejected') | -                  | 승인/거절      |
| reason       | -  | ❌        | TEXT                        | NULL               | 사유(거절일 경우) |
| created\_at  | -  | ✅        | DATETIME                    | CT | 처리 일시      |

---

# 📌 MVP 이후 확장 설계

- 댓글 공감/신고 기능은 MVP 이후 확장 가능
- GPT 질문 외에 사용자 제안 질문이 커뮤니티 주축이 될 수 있도록 밸런스 조정 필요


