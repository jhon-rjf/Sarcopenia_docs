# Clova Voice (TTS) 서비스 가이드

## 개요
Clova Voice는 네이버 클라우드 플랫폼의 Text-to-Speech(TTS) 서비스입니다. 텍스트를 자연스러운 음성으로 변환해주는 기능을 제공합니다.
현재는 회원가입시 제공받는 크레딧 100,000원을 사용하고 있으며, 서비스 처음 가입시 90,000원의 비용이 발생하였습니다. 가입 기간은 1개월입니다.

## 설정 방법

### 1. 네이버 클라우드 플랫폼 설정
1. [네이버 클라우드 플랫폼](https://www.ncloud.com/) 접속 및 로그인
2. AI Service > Clova Voice > Premium 상품 선택
3. 서비스 신청 및 API 인증 정보 획득
   - Client ID
   - Client Secret

### 2. 프로젝트 파일 설정

#### 2.1 환경 변수 설정
프로젝트 루트 디렉토리에 `.env` 파일 생성:
```
CLOVA_VOICE_CLIENT_ID=your_client_id_here
CLOVA_VOICE_CLIENT_SECRET=your_secret_key_here
```
-> 추후 이 부분에 client id와 key를 변경하면 다른 계정으로 사용가능 합니다.
#### 2.2 필요한 파일 수정
1. `app/utils/speech_recognition.py`:
   - ClovaTTS 클래스 구현
   - TTS API 연동 로직

2. `app/routers/input_handler.py`:
   - TTS 라우터 설정
   - 환경 변수 로드
   - API 엔드포인트 구현

3. `app/main.py`:
   - CORS 설정
   - 라우터 등록
   - 환경 변수 로드 확인

## API 사용법

### 엔드포인트
```
POST /api/tts
```

### 요청 형식
```json
{
    "text": "변환할 텍스트"
}
```

### 응답
- 성공: MP3 형식의 오디오 파일
- 실패: 에러 메시지

### 파라미터 설정
- speaker: "nara" (한국어 여성)
- volume: 0 (기본값, 범위: -5 ~ 5)
- speed: 0 (기본값, 범위: -5 ~ 5)
- pitch: 0 (기본값, 범위: -5 ~ 5)
- format: "mp3" (지원 형식: mp3, wav)

## 사용 예시

### Python
```python
import requests

def test_tts():
    url = "http://localhost:8000/api/tts"
    data = {"text": "안녕하세요"}
    
    response = requests.post(url, json=data)
    
    if response.status_code == 200:
        with open("output.mp3", "wb") as f:
            f.write(response.content)
        print("TTS 변환 성공!")
    else:
        print(f"에러 발생: {response.text}")
```

### JavaScript (frontend/static/js/api.js)
```javascript
async function testTTS() {
    try {
        const response = await fetch('http://localhost:8000/api/tts', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ text: '안녕하세요' })
        });

        if (response.ok) {
            const audioBlob = await response.blob();
            const audioUrl = URL.createObjectURL(audioBlob);
            const audio = new Audio(audioUrl);
            audio.play();
        }
    } catch (error) {
        console.error('에러:', error);
    }
}
```

## 제한 사항
- 최대 텍스트 길이: 1000자
- 지원 언어: 한국어
- 출력 형식: MP3, WAV

## 에러 코드
- 503: TTS 서비스 사용 불가 (API 키 누락)
- 500: TTS 변환 중 오류 발생
- 400: 잘못된 요청 (텍스트 누락 등)
