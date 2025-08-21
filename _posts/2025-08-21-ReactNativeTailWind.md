---
layout: single
title: "React Native Windows + Tailwind(NativeWind) 설정 가이드"
categories: [Frontend]
tags: [React Native, Windows, Tailwind, NativeWind]
---

# React Native Windows + Tailwind(NativeWind) 설정 가이드

## 1. 설치

프로젝트 루트에서:
```bash
# Tailwind 런타임
npm i nativewind tailwindcss

# (최초 1회) Tailwind 설정 파일 생성
npx tailwindcss init
```

---

## 2. Babel 설정

babel.config.js 
```js
module.exports = {
  plugins: ['nativewind/babel'],
};
```

---

## 3. Tailwind 설정 파일
tailwind.config.js (또는 .cjs)
```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './App.{js,jsx,ts,tsx}',
    './src/**/*.{js,jsx,ts,tsx}',
  ],
  theme: {
    extend: {
      colors: {
        brand: {
          50:  '#f0f7ff',
          500: '#3b82f6',
          600: '#2563eb',
        },
        fontFamily: {
          sans: ['Noto Sans KR'],
        },
      },
    },
  },
  plugins: [],
};
```

---

## 4. TypeScript(선택)

tsconfig.json
```json
{
  "compilerOptions": {
    "types": ["nativewind/types"]
  }
}
```

또는 src/global.d.ts에:
```ts
/// <reference types="nativewind/types" />
```
---
