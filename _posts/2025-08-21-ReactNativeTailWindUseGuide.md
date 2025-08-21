---
layout: single
title: "React Native Windows에서 Tailwind(NativeWind) 핵심 문법 & 사용 가이드"
categories: [Frontend]
tags: [React Native, Windows, Tailwind, NativeWind]
---

# 📌 Tailwind(NativeWind) 문법 사용 규칙

- 모든 RN 컴포넌트에 className 사용 가능
- 값 단위: Tailwind 스케일 → RN의 px 로 적용 (p-4 = 16px 등)
- 다크모드: OS 테마를 따름 (dark: 프리픽스 사용)
- 플랫폼/방향/화면방향/상태 등의 Variant 지원 (아래 참고)

## 1. 레이아웃/Flex
```tsx
<View className="flex-1 p-4 gap-4">
  <View className="flex-row items-center justify-between">
    <Text className="text-xl font-semibold">Title</Text>
    <View className="w-8 h-8 rounded-full bg-brand-500" />
  </View>

  <View className="flex-1 rounded-2xl bg-white p-4" />
</View>
```
### 자주 쓰는 클래스:
- flex, flex-1, flex-row, flex-col(기본)
- items-start/center/end, justify-start/center/between/around
- gap-2/4/6 (자식 간 간격)
- p-4, px-4, py-2, m-2, mt-4 …

---

## 2. 색상/배경/테두리/둥근모서리

```tsx
<View className="bg-white rounded-2xl border border-neutral-200 p-4">
  <Text className="text-brand-600">Hello</Text>
</View>
```
- 배경: bg-white, bg-neutral-50, bg-brand-500
- 텍스트: text-black, text-neutral-600, text-brand-600
- 테두리: border, border-2, border-brand-500, border-t
- 라운드: rounded, rounded-lg, rounded-2xl, rounded-full
> 커스텀 컬러는 tailwind.config.js의 theme.extend.colors에 정의
---

## 3.타이포그래피
```tsx
<Text className="text-xs" />
<Text className="text-sm" />
<Text className="text-base" />
<Text className="text-lg" />
<Text className="text-xl font-bold tracking-tight leading-snug" />
```
- 굵기: font-thin ~ font-black
- 간격: tracking-tight/normal/wide (letterSpacing), leading-4/5/... (lineHeight)
- 정렬: text-left/center/right

---

## 4. 크기/간격/포지션

```tsx
<View className="w-12 h-12" />
<View className="h-[72px] w-[72px]" />         {/* 임의 값(Arbitrary) */}
<View className="absolute top-4 right-4 z-10" />
```
- 폭/높이: w-, h-, min-w-, max-h-
- 절대/상대: absolute, relative, inset-0, top-2, left-4
- Z-Index: z-0/10/20/...
> 임의 값 지원: w-[120px], bg-[#123456], rounded-[14px] 등.

---

## 5. 그림자/불투명도/블러

```tsx
<View className="bg-white p-4 rounded-2xl shadow-md" />
<View className="opacity-60" />
```
- shadow, shadow-sm/md/lg/xl(플랫폼별 지원 차이 있음. Windows에선 제한될 수 있어 스타일로 보완 가능)
- 투명도: opacity-0 ~ 100
- 블러(배경): RN 자체엔 없음 → 별도 라이브러리 사용

---

## 6. 조건부 클래스 패턴

```tsx
// 간단
<View className={`p-4 ${error ? 'bg-red-50 border border-red-300' : 'bg-white'}`} />

// 유틸 함수(cnx) (선택)
import clsx from 'clsx';
const cn = (...args: any[]) => clsx(args);

<View className={cn('p-4', error ? 'bg-red-50' : 'bg-white')} />
```
---

## 7. NativeWind 스타일링 방법 2가지

- (1) className 직사용 (권장)

```tsx
<Text className="text-base text-neutral-700">Hello</Text>
```

- (2) styled()로 서드파티 컴포넌트 감싸기

```tsx
import {styled} from 'nativewind';
import {ActivityIndicator} from 'react-native';

const Spinner = styled(ActivityIndicator);
<Spinner className="text-brand-500" />
```
---