---
layout: single
title: "GitHub CLI와 Git 사용자 계정 확인 명령어 모음"
categories: Git
tags: [GitHub, Git, CLI, 계정관리]
toc: true
toc_sticky: true
author_profile: true
---

# 📌 개요

Git과 GitHub CLI를 사용하다 보면 **계정 충돌**이나 **권한 오류**가 발생할 수 있습니다.  
그럴 때 유용한 GitHub CLI 및 Git 관련 계정 확인/설정 명령어를 정리했습니다.

---

# 📌 GitHub CLI 관련 명령어 (`gh`)

| 명령어                             | 설명                                              |
| ---------------------------------- | ------------------------------------------------- |
| `gh auth status`                  | 현재 로그인된 GitHub 계정 상태 확인               |
| `gh auth login --hostname github.com` | GitHub 계정 로그인 (CLI 인증)                     |
| `gh auth switch`                  | 여러 계정 간 전환                                 |

---

# 📌 Git 기본 정보 확인 명령어

| 명령어                    | 설명                                       |
| ------------------------- | ------------------------------------------ |
| `git config user.name`    | 현재 Git에 설정된 사용자 이름 확인         |
| `git config user.email`   | 현재 Git에 설정된 사용자 이메일 확인       |
| `git config`              | 전체 Git 설정 정보 확인 (로컬/글로벌 포함) |

---

# 📌 Git 원격 저장소 관련 명령어

| 명령어            | 설명                                |
| ----------------- | ----------------------------------- |
| `git remote -v`   | 연결된 원격 저장소(URL) 정보 확인   |

---

# 📌 사용 예시

```bash
# 현재 GitHub CLI 인증 상태 확인
gh auth status

# GitHub 계정 로그인
gh auth login --hostname github.com

# CLI에서 로그인된 여러 계정 전환
gh auth switch

# Git 사용자 정보 확인
git config user.name
git config user.email

# Git 전체 설정 확인
git config

# 현재 연결된 GitHub 원격 저장소 확인
git remote -v
```