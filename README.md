# MK Tech Blog

기술과 기록이 만나는 공간, **MK Tech Blog**입니다.  
이 블로그는 개발자의 경험과 배움을 공유하고, 프로젝트 아카이빙 및 기술 문서를 정리하기 위해 만들어졌습니다.  
`Minimal Mistakes` Jekyll 테마를 기반으로 커스터마이징하였습니다.

## 📌 주요 기능

- 기술 블로그, 사이드 프로젝트 정리
- 태그 및 카테고리별 분류 아카이브
- 연도별 아카이브 제공
- 커스텀 배경 및 테마 스타일링
- 반응형 레이아웃
- GitHub Pages로 배포

---

## 🛠 로컬 실행 방법

이 프로젝트는 Jekyll 기반의 정적 사이트입니다.

### 1. 저장소 클론

```bash
git clone https://github.com/munkyu87/munkyu87.github.io.git
cd munkyu87.github.io
```

### 2. 필요한 gem 설치

```bash
bundle install
```

### 3. 로컬 서버 실행

```bash
rm -rf _site
bundle exec jekyll serve
```

→ 브라우저에서 http://localhost:4000 열면 블로그 확인 가능
→ 실시간 반영을 원할 경우:

```bash
bundle exec jekyll serve --livereload
```
