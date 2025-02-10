# Clova Speech (STT) 서비스 가이드

## 개요
Clova Speech는 네이버 클라우드 플랫폼의 Speech-to-Text(STT) 서비스입니다. 음성을 텍스트로 변환해주는 기능을 제공합니다.

## 설정 방법

### 1. 네이버 클라우드 플랫폼 설정
1. [네이버 클라우드 플랫폼](https://www.ncloud.com/) 접속 및 로그인
2. AI Service > Clova Speech > Custom Speech Recognition 선택
3. API Gateway 생성
4. API 인증 정보 획득:
   - Secret Key
   - Invoke URL

### 2. 프로젝트 파일 설정

#### 2.1 환경 변수 설정
프로젝트 루트 디렉토리에 `.env` 파일 생성 또는 수정:
```
CLOVA_SPEECH_SECRET=your_secret_key_here
CLOVA_SPEECH_INVOKE_URL=your_invoke_url_here
```

#### 2.2 필요한 파일 수정
1. `app/utils/speech_recognition.py`:
   - ClovaSTT 클래스 구현
   - STT API 연동 로직

2. `app/routers/input_handler.py`:
   - STT 라우터 설정
   - 환경 변수 로드
   - API 엔드포인트 구현

## API 사용법

### 엔드포인트
```
POST /api/speech
```

### 요청 형식
- Content-Type: multipart/form-data
- Body: audio file (mp3, wav 형식)

### 응답 형식
```json
{
    "recognized_text": "인식된 텍스트",
    "response": "챗봇 응답"
}
```

### 파라미터 설정
- language: "ko-KR" (한국어)
- completion: "sync" (동기 처리)
- format: "mp3" 또는 "wav"

## 사용 예시

### Python
```python
import requests

def test_stt():
    url = "http://localhost:8000/api/speech"
    
    with open("audio_file.mp3", "rb") as f:
        files = {"audio_file": f}
        response = requests.post(url, files=files)
    
    if response.status_code == 200:
        result = response.json()
        print(f"인식된 텍스트: {result['recognized_text']}")
        print(f"챗봇 응답: {result['response']}")
    else:
        print(f"에러 발생: {response.text}")
```

### JavaScript
```javascript
async function testSTT() {
    try {
        const audioFile = // 오디오 파일 (녹음 또는 파일 선택)
        const formData = new FormData();
        formData.append('audio_file', audioFile);

        const response = await fetch('http://localhost:8000/api/speech', {
            method: 'POST',
            body: formData
        });

        if (response.ok) {
            const result = await response.json();
            console.log('인식된 텍스트:', result.recognized_text);
            console.log('챗봇 응답:', result.response);
        }
    } catch (error) {
        console.error('에러:', error);
    }
}
```

## 제한 사항
- 지원 파일 형식: MP3, WAV
- 최대 파일 크기: 30MB
- 지원 언어: 한국어
- 최대 음성 길이: 10분

## 에러 코드
- 503: STT 서비스 사용 불가 (API 키 누락)
- 500: STT 변환 중 오류 발생
- 400: 잘못된 요청 (파일 누락 등)

