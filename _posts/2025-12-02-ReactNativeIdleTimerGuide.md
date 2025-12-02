---
layout: single
title: "React Native Idle Timer로 일정 시간 후 홈으로 자동 이동시키는 방법"
categories: [Frontend]
tags: [React Native, Idle Timer, Auto Navigation]
---

# React Native Idle Timer 구현 가이드  
사용자가 **일정 시간 동안 아무 행동도 하지 않으면 자동으로 홈 화면으로 이동시키는 기능**은  
키오스크·공공 단말기·전시용 앱 등에서 자주 쓰이는 UX 패턴이다.

React Native에서는 **Idle Timer(비활성 타이머) 방식**이 가장 일반적이며,  
터치·스크롤·입력 등 사용자의 모든 행동을 기반으로 타이머를 리셋시키고,  
지정된 시간이 지나면 자동 이동을 수행한다.

---

# 📌 Idle Timer 핵심 개념
- 화면이 나타나면 타이머 시작
- 사용자가 터치/입력하면 타이머 초기화(reset)
- 지정된 시간 동안 아무 행동도 없으면 자동으로 특정 화면으로 이동
- 앱 상태(AppState: active/background) 변화에도 대응

---

## 1. Idle Timer 훅 구현 (`useIdleTimer`)
```tsx
import { useEffect, useRef } from 'react';
import { AppState } from 'react-native';
import { useNavigation } from '@react-navigation/native';

const TIMEOUT = 60000; // 60초 임의 설정 가능

export function useIdleTimer() {
  const navigation = useNavigation();
  const timerRef = useRef<NodeJS.Timeout | null>(null);

  const resetTimer = () => {
    if (timerRef.current) clearTimeout(timerRef.current);

    timerRef.current = setTimeout(() => {
      navigation.navigate('Home');
    }, TIMEOUT);
  };

  useEffect(() => {
    // 화면 진입 시 초기 타이머 시작
    resetTimer();

    // AppState 변화(active/background)에도 타이머 리셋
    const subscription = AppState.addEventListener("change", resetTimer);

    return () => {
      // 언마운트 시 타이머/리스너 정리
      if (timerRef.current) clearTimeout(timerRef.current);
      subscription.remove();
    };
  }, []);

  return { resetTimer };
}
```

---

## 2. Idle Timer를 적용하는 방법
특정 화면(SomeScreen)에서 사용자 입력(터치 등)이 발생하면
타이머를 리셋하여 비활성 시간을 다시 계산한다.

```tsx
const SomeScreen = () => {
  const { resetTimer } = useIdleTimer();

  return (
    <TouchableWithoutFeedback onPress={resetTimer}>
      <View>
        {/* 화면 UI */}
      </View>
    </TouchableWithoutFeedback>
  );
};

export default SomeScreen;
```

TextInput 같은 키보드 입력도 감지 가능.
```tsx
<TextInput
  value={value}
  onChangeText={(text) => {
    resetTimer();
    setValue(text);
  }}
  className="border p-3 rounded-lg"
/>
```

---

## 3. 전체 동작 흐름 (Flow)

```bash
1) SomeScreen 컴포넌트 마운트됨
      ↓
2) useIdleTimer() 실행
      ↓
3) useEffect() 최초 실행
      ↓
4) resetTimer() 자동 호출 → 첫 타이머 시작
      ↓
5) 아무 행동 없으면 → TIMEOUT(60초) 후 'Home' 으로 자동 navigate
      ↓
6) 사용자가 터치/입력 시 → resetTimer() 다시 호출 → 타이머 리셋
```

---

## 4. 실무 관점: Idle Timer는 성능 이슈 거의 없음
- setTimeout/clearTimeout은 매우 가벼움
- RN은 기본적으로 모든 터치 이벤트를 이미 감지하고 있음
- 렌더링과 무관한 로직이므로 큰 부하 없음
- 키오스크·공공앱·전시앱에서도 동일 방식 사용

---

<div class="ads-area in-article">
  <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-3629284805087203"
      crossorigin="anonymous"></script>
  <!-- in-article-1 -->
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

## 5. MobX IdleTimerStore로 전역 Idle Timer 구현하기 (권장)
Hook 방식은 간단한 화면에서는 충분하지만,
앱 전체에서 Idle 기능을 사용할 때는 MobX Store 기반 구조가 훨씬 안정적이다.
- 모든 화면에서 이벤트를 통합 관리 가능
- 타이머 중복 생성 위험 없음
- 특정 화면에서 Idle 기능 켜고 끄기 쉬움
- Timeout 시간을 사용자 설정으로 변경 가능
- TextInput / Gesture / Scroll 모두 한 지점에서 제어 가능
규모 큰 앱에서는 스토어 방식이 사실상 정답.

---

### 5-1. IdleTimerStore 구현

```ts
import { makeAutoObservable } from "mobx";
import { AppState } from "react-native";
import { navigationRef } from "../navigation/NavigationService";

class IdleTimerStore {
  timeout = 60000; // 전체 앱 기본 Idle 시간
  timer: NodeJS.Timeout | null = null;
  enabled = false;

  constructor() {
    makeAutoObservable(this);

    // AppState 변화 시 Idle Timer 리셋
    AppState.addEventListener("change", () => {
      if (this.enabled) this.reset();
    });
  }

  start() {
    this.enabled = true;
    this.reset();
  }

  stop() {
    this.enabled = false;
    if (this.timer) clearTimeout(this.timer);
  }

  reset() {
    if (!this.enabled) return;

    if (this.timer) clearTimeout(this.timer);
    this.timer = setTimeout(() => {
      navigationRef.navigate("Home");
    }, this.timeout);
  }

  setTimeoutValue(ms: number) {
    this.timeout = ms;
    this.reset(); // 변경 값 즉시 반영
  }
}

export const idleTimerStore = new IdleTimerStore();
```

---

### 5-2. 특정 화면에서 Idle 기능 활성/비활성

```tsx
import { useEffect } from "react";
import { idleTimerStore } from "../stores/IdleTimerStore";

function SomeScreen() {
  useEffect(() => {
    idleTimerStore.start();   // 화면 진입 시 IdleTimer 활성
    return () => idleTimerStore.stop(); // 종료 시 비활성
  }, []);

  return (
    <TouchableWithoutFeedback onPress={() => idleTimerStore.reset()}>
      <View>{/* 화면 UI */}</View>
    </TouchableWithoutFeedback>
  );
}
```

---

### 5-3. TextInput 입력도 자동 감지

```tsx
<TextInput
  value={value}
  onChangeText={(text) => {
    idleTimerStore.reset();
    setValue(text);
  }}
/>
```
