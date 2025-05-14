---
layout: single
title: "Google Cloud TTS 연동 가이드"
categories: AI
tags: [TTS, Google Cloud]
toc: true
toc_sticky: true
author_profile: true
---

# 📌 개요

이 문서는 **Google Cloud Text-to-Speech API**를 활용해, Java Spring Boot 기반 백엔드에서 텍스트를 자연스러운 음성(MP3)으로 변환하는 과정을 정리한 가이드입니다.

Google의 신경망 기반 음성 합성 API를 사용하여, 원하는 텍스트를 실시간으로 음성 파일로 변환합니다.

- **사용 기술**: Google Cloud TTS API
- **개발 언어**: Java (Spring Boot)
- **파일 포맷**: MP3
- **보안 방식**: 서비스 계정 기반 인증 (JSON Key 사용)

---

# 📌 전체 흐름 요약

| 단계 | 설명                                                             |
| ---- | ---------------------------------------------------------------- |
| 1    | Google Cloud 콘솔에서 TTS API 활성화 및 서비스 계정 키 생성      |
| 2    | 서비스 계정 키(.json) 파일을 프로젝트에 저장 (`.gitignore` 필수) |
| 3    | Spring에서 TTSClient Bean 구성 및 인증 적용                      |
| 4    | Controller에서 텍스트 입력 → 음성 바이트 반환 API 구성           |
| 5    | 클라이언트는 해당 API를 호출하여 음성 스트리밍 또는 저장         |

---

# 📌 서비스 계정 키 생성

1. Google Cloud Console 접속
2. 프로젝트 선택 → “Text-to-Speech API” 활성화
3. IAM & 관리자 > 서비스 계정
4. 새 계정 생성 → 역할: Text-to-Speech 사용자
5. 키 추가 → **JSON 형식 다운로드**

---

# 📌 application.yml 설정

```yaml
tts:
  google:
    credentials-path: src/main/resources/google/tts-key.json
```

---

# 📌 Java 구현 예시

## 🔽 인증 및 Bean 구성 (TtsConfig.java)

```java
@Configuration
public class TtsConfig {

    @Value("${tts.google.credentials-path}")
    private String credentialsPath;

    @Bean
    public TextToSpeechClient textToSpeechClient() throws Exception {
        GoogleCredentials credentials = GoogleCredentials.fromStream(new FileInputStream(credentialsPath));
        TextToSpeechSettings settings = TextToSpeechSettings.newBuilder()
            .setCredentialsProvider(FixedCredentialsProvider.create(credentials))
            .build();

        return TextToSpeechClient.create(settings);
    }
}
```

---

## 🔽 TTS 서비스 로직

```java
@Service
@RequiredArgsConstructor
public class TTSService {

    private final TextToSpeechClient textToSpeechClient;

    public byte[] synthesizeSpeech(String text, String genderInput) throws Exception {
        SynthesisInput input = SynthesisInput.newBuilder().setText(text).build();

        VoiceSelectionParams voice = VoiceSelectionParams.newBuilder()
            .setLanguageCode("ko-KR")
            .setSsmlGender(SsmlVoiceGender.FEMALE)
            .build();

        AudioConfig audioConfig = AudioConfig.newBuilder()
            .setAudioEncoding(AudioEncoding.MP3)
            .build();

        SynthesizeSpeechResponse response = textToSpeechClient.synthesizeSpeech(input, voice, audioConfig);
        return response.getAudioContent().toByteArray();
    }
}
```

---

## 🔽 API Controller 구성

```java
@RestController
@RequestMapping("/tts")
@RequiredArgsConstructor
public class TTSController {

    private final TTSService ttsService;

    @GetMapping("/speak")
    public ResponseEntity<ByteArrayResource> speak(@RequestParam(required = false) String gender) {
        try {
            byte[] mp3Data = ttsService.synthesizeSpeech(gender);
            ByteArrayResource resource = new ByteArrayResource(mp3Data);

            return ResponseEntity.ok()
                    .header(HttpHeaders.CONTENT_DISPOSITION, "inline; filename=\"speech.mp3\"")
                    .contentType(MediaType.APPLICATION_OCTET_STREAM)
                    .contentLength(mp3Data.length)
                    .body(resource);
        } catch (Exception e) {
            return ResponseEntity.internalServerError().build();
        }
    }
}
```

---

# 📌 테스트 예시

- http://localhost:8080/tts/speak?text=기사 상세 부동산 사기 당한 내 전셋집이 월세로 나왔어요 뉴스 요약쏙 AI 요약...
  - mp3파일로 응답!
