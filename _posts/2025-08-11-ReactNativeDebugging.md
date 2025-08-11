---
layout: single
title: "React Native Windows 디버깅 가이드 (Native & JS)"
categories: [Frontend]
tags: [React Native, Windows, UWP, Debugging]
---

# React Native Windows 디버깅 가이드 (Native & JS)

> 환경: React Native for Windows (Hermes), Visual Studio 2022, VS Code + React Native Tools

--- 

## 네이티브(C#) 디버깅 — Visual Studio
1. windows/WindowsApp.sln 열기 → 시작 프로젝트를 WindowsApp로 설정
2. 상단 툴바에서 Debug | x64 선택
3. 아래 위치에 브레이크포인트 설정
	- windows/**/App.xaml.cs의 App() 생성자 또는 OnLaunched
	- windows/**/MainPage.xaml.cs의 생성자(MainPage()), OnNavigatedTo
4. F5 실행 → 해당 지점에서 중단되면 성공

---

## JS(Hermes) 디버깅 — VS Code (React Native Tools)
### A. 준비
- VS Code 확장: React Native Tools 설치
- 프로젝트 루트(package.json 있는 곳)에 .vscode/launch.json 생성:
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Hermes (RN Windows)",
      "type": "reactnativedirect",
      "request": "attach",
      "platform": "windows",
      "cwd": "${workspaceFolder}",
      "port": 8081
    }
  ]
}
```

### B. 실행 순서

1. Metro 시작
```powershell
npx react-native start
```
2. Windows 앱 실행
- Visual Studio에서 F5(또는 npx react-native run-windows)
3. 앱 Dev Menu (Ctrl+Shift+D) → 상태 확인
- Disable Direct Debugging가 보이면 = 이미 ON (좋음)
- 필요 시 Enable Break on First Line 활성화
4. VS Code 디버그 패널에서 Attach to Hermes (RN Windows) 선택 → F5
5. TS/JS 파일에 브레이크포인트를 걸고, 앱에서 해당 코드 경로를 실행
- 즉시 멈추게 하려면 코드에 debugger; 한 줄 추가 후 Reload Javascript
```ts
// App.tsx 예시
useEffect(() => {
  debugger;
  console.log('[JS] mounted');
}, []);
```
> Hermes에선 “Open JS Debugger” 메뉴가 없고, Direct Debugging + Attach 방식이 정석입니다.
