---
layout: single
title: "MSSQL 잠금과 sp_lock"
categories: Database
tags: [MSSQL, Lock]
toc: true
toc_sticky: true
author_profile: true
---

# 📌 MSSQL 잠금과 `sp_lock`

MSSQL에서는 동시에 여러 사용자가 데이터를 처리하기 때문에 "잠금(Lock)"은 필수적인 동시성 제어 메커니즘입니다. 이 문서에서는 `sp_lock`, 잠금 모드, 충돌 여부, 그리고 블로킹 추적 및 해결 방법까지 실무에서 필요한 내용을 정리합니다.

---

# ✅ `sp_lock` 이란?

SQL Server의 시스템 저장 프로시저로, **현재 세션이 획득하고 있는 잠금 정보**를 보여줍니다.

```sql
EXEC sp_lock;
```

> ⚠️ 최신 시스템에서는 `sys.dm_tran_locks` 뷰를 사용하는 것이 더 정밀하고 권장됩니다.

---

# ✅ Lock Mode 종류 및 의미

| 모드    | 이름                      | 설명                                                  |
| ------- | ------------------------- | ----------------------------------------------------- |
| `S`     | Shared                    | 읽기 전용 (SELECT) 시 사용                            |
| `X`     | Exclusive                 | 쓰기 (INSERT/UPDATE/DELETE) 시 사용                   |
| `U`     | Update                    | 추후 `X`로 바뀔 수 있는 중간 락 (예: WHERE 후 UPDATE) |
| `IS`    | Intent Shared             | 하위 리소스에 `S` 락 걸 의도 표시                     |
| `IX`    | Intent Exclusive          | 하위 리소스에 `X` 락 걸 의도 표시                     |
| `SIX`   | Shared + Intent Exclusive | 읽기하면서 하위에 쓰기하려는 복합 락                  |
| `Sch-S` | Schema Stability          | SELECT 시 테이블 구조 변경 방지를 위한 락             |
| `Sch-M` | Schema Modification       | DDL 작업(ALTER, DROP 등) 시 사용                      |
| `BU`    | Bulk Update               | 대량 삽입 시 사용 (BULK INSERT 등)                    |

---

# ✅ 락 모드 충돌 표

|         | S   | U   | X   | IS  | IX  | SIX |
| ------- | --- | --- | --- | --- | --- | --- |
| **S**   | ✅  | ❌  | ❌  | ✅  | ❌  | ❌  |
| **U**   | ❌  | ❌  | ❌  | ✅  | ❌  | ❌  |
| **X**   | ❌  | ❌  | ❌  | ❌  | ❌  | ❌  |
| **IS**  | ✅  | ✅  | ❌  | ✅  | ✅  | ❌  |
| **IX**  | ❌  | ❌  | ❌  | ✅  | ✅  | ❌  |
| **SIX** | ❌  | ❌  | ❌  | ❌  | ❌  | ❌  |

✅ = 호환 가능, ❌ = 충돌 발생

---

# ✅ `sp_lock` 실행 결과 예시 해석

| 컬럼       | 설명                                         |
| ---------- | -------------------------------------------- |
| `spid`     | 세션 ID                                      |
| `dbid`     | 데이터베이스 ID                              |
| `ObjId`    | 잠금 대상 객체 (테이블 등) ID                |
| `Type`     | 락 대상 리소스 종류 (RID, KEY, PAG 등)       |
| `Resource` | 리소스 식별자 (인덱스 키 값, 페이지 번호 등) |
| `Mode`     | 락 모드 (S, X, IX 등)                        |
| `Status`   | 현재 상태 (GRANT=획득됨, WAIT=대기 중)       |

---

# ✅ 락 충돌 원인 추적

```sql
-- 블로킹 세션 추적
SELECT
    r.session_id,
    r.blocking_session_id,
    r.wait_type,
    r.wait_time,
    r.status,
    t.text AS running_sql
FROM sys.dm_exec_requests r
JOIN sys.dm_exec_sessions s ON r.session_id = s.session_id
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) t
WHERE r.blocking_session_id <> 0;
```

```sql
-- 특정 세션의 실행 SQL 확인
DBCC INPUTBUFFER(<spid>);
```

---

# ✅ 세션 종료 (KILL)

장시간 대기 중이거나 블로킹 중인 세션은 종료할 수 있습니다.

```sql
KILL <spid>;
```

> ⚠️ 트랜잭션을 강제로 종료하므로 신중히 사용하세요.

---

# ✅ 락 리소스 타입 종류

| 타입       | 설명                   |
| ---------- | ---------------------- |
| `RID`      | Row ID (개별 레코드)   |
| `KEY`      | 인덱스 키              |
| `PAG`      | 페이지 (8KB)           |
| `EXT`      | Extent (8 페이지 단위) |
| `TAB`      | 테이블 전체            |
| `DB`       | 전체 DB 수준           |
| `METADATA` | 시스템 메타데이터      |

---

# ✅ 성능 이슈와 해결 전략

- **조회 쿼리에도 Sch-S 락이 걸림** → 스키마 구조 변경 중인 세션과 충돌 주의
- **인덱스 미비 시 RID, PAG 등 하위 수준에 과도한 락 발생**
- **NOLOCK은 Dirty Read의 위험**

```sql
-- NOLOCK 예시
SELECT * FROM Orders WITH (NOLOCK);
```

- **락이 오래 지속되는 이유**:
  - BEGIN TRANSACTION 후 COMMIT 없이 대기
  - 커서 또는 사용자 대기 상태

---

# ✅ 실무 체크리스트

- [x] 블로킹 세션 있으면 `sp_who2`, `dm_exec_requests`로 추적
- [x] `DBCC INPUTBUFFER(spid)`로 SQL 확인
- [x] 필요한 경우 `KILL`로 종료
- [x] SELECT 쿼리에도 `Sch-S` 락 발생 가능
- [x] `WITH (NOLOCK)`은 조심해서 사용
- [x] 지속적인 블로킹은 인덱스, 쿼리 구조 개선 필요
