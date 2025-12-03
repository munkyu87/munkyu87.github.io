---
layout: single
title: "React Native에서 ‘렌더링 없이 변화만 감지’하는 Subscribe 패턴 정리"
categories: [Frontend]
tags: [React Native, 상태관리, subscribe, reaction, autorun]
---

React Native에서 렌더링 없이 상태 변화만 감지하고 싶을 때는
상태관리 라이브러리마다 사용하는 방식과 명칭이 다르다.

Idle Timer나 Background Logic 구현 시 꼭 알아두면 좋은 개념.

---

# 1. React 자체에서의 Subscribe (렌더링 없이 감지만)
## useRef + 이벤트 리스너 방식
- 상태 변해도 리렌더가 일어나지 않음
- 가장 기본적인 subscribe 패턴

```tsx
const handlerRef = useRef<any>(null);

useEffect(() => {
  handlerRef.current = someEventEmitter.addListener((v) => {
    console.log("변화 감지", v); // 렌더링 X
  });

  return () => handlerRef.current?.remove();
}, []);
```
→ 명칭
- Event Listener
- Custom Subscribe

---

# 2. MobX에서의 Subscribe
reaction() : 가장 대표적인 MobX용 subscribe 렌더링과 완전히 분리된 감시(Watcher) 기능
```tsx
reaction(
  () => store.value,     // 감시
  (v) => console.log(v)  // 값 변경 시 호출 (렌더링 X)
);
```
autorun() : 전체 추적 기반 자동 실행
```tsx
autorun(() => {
  console.log(store.value);
});
```
→ 명칭
- MobX Reaction (1개 값 감시)
- MobX Autorun (여러 값 자동 추적)
- TextInput 같은 키보드 입력도 감지 가능.

---

# 3. Zustand의 Subscribe
Zustand는 subscribe 기능이 매우 강력함.
렌더링과 완전 독립적으로 동작함.

```tsx
const unsub = useStore.subscribe(
  (state) => state.value,
  (value) => console.log("변경됨", value) // 렌더링 X
);
```
→ 명칭:
- Zustand subscribe
- Selector-based Subscription

---

# 4. Redux(또는 Redux Toolkit)의 Subscribe
컴포넌트와 리렌더링 없이 Store 변경 감지 가능.

```tsx
store.subscribe(() => {
  const state = store.getState();
  console.log("Redux 상태 변경", state);
});
```
→ 명칭
- Redux Store Subscribe

# 5. EventEmitter(React Native 기본 API)
RN은 네이티브와 통신하거나 전역 이벤트 쓸 때 EventEmitter 사용함.

```tsx
const emitter = new NativeEventEmitter();

useEffect(() => {
  const sub = emitter.addListener("MY_EVENT", (v) => {
    console.log("이벤트:", v);
  });

  return () => sub.remove();
}, []);
```
→ 명칭
- EventEmitter Listener
- Native Event Subscription

---

# 6. 상태관리별 Subscribe 방식 비교

| 상태관리 방식 | 명칭                                | 설명 |
| ------------- | ----------------------------------- | ----- |
| React (useRef) | 이벤트 리스너 / 커스텀 subscribe       | 가장 기본적인 비렌더 구독 방식 |
| MobX          | reaction / autorun                   | 상태 감시에 특화된 MobX 전용 Subscribe API |
| Zustand       | store.subscribe                      | React 렌더링과 완전히 독립된 상태 구독 |
| Redux         | store.subscribe                      | Redux 스토어 변경 감지용 classic subscribe |
| EventEmitter  | NativeEventEmitter listener          | 네이티브 레벨 이벤트 감지에 사용 |

---