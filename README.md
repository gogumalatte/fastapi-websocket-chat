# 🗨️ 실시간 웹소켓 채팅 서비스

이 프로젝트는 **FastAPI + WebSocket + React**를 활용하여 구현한 **실시간 채팅 웹 서비스**입니다. 프론트엔드와 백엔드가 각각 역할을 분담하며, 웹소켓 기반의 양방향 통신을 통해 사용자 간 실시간 메시지 송수신이 가능합니다.

<br/>

## 📌 개요

- 실시간 채팅이 가능한 경량 서비스
- Python 기반의 FastAPI로 서버 구성
- WebSocket을 통한 빠르고 효율적인 통신
- React로 사용자 친화적인 채팅 인터페이스 구성
- Docker로 배포 및 테스트 가능

<br/>

## ⚙️ 기술 스택

| 영역 | 기술 |
|------|------|
| Frontend | React, JavaScript |
| Backend | Python, FastAPI, WebSocket |
| Infra | Docker, Ngrok (테스트용 외부 접근) |

<br/>

## 🎯 핵심 기능

- ✅ **웹소켓 기반 실시간 채팅**
- ✅ **클라이언트와 서버 간 비동기 메시지 처리**
- ✅ **멀티 유저 연결 처리**
- ✅ **연결 상태 추적 및 브로드캐스팅**
- ✅ **터미널 출력으로 메시지 흐름 확인 가능**

<br/>

## 🧠 핵심 알고리즘 및 동작 흐름

### 1. 백엔드 (FastAPI + WebSocket)

- `/ws/{client_id}` 경로를 통해 웹소켓 연결 수립
- 연결된 클라이언트를 `connection_manager` 클래스가 관리:
  - `connect()`: 접속 시 클라이언트 등록
  - `disconnect()`: 접속 종료 시 클라이언트 제거
  - `broadcast()`: 모든 사용자에게 메시지 전달
- 메시지를 수신하면 해당 메시지를 모든 접속자에게 브로드캐스트

```python
@app.websocket("/ws/{client_id}")
async def websocket_endpoint(websocket: WebSocket, client_id: str):
    await manager.connect(websocket, client_id)
    try:
        while True:
            data = await websocket.receive_text()
            await manager.broadcast(f"{client_id}: {data}")
    except WebSocketDisconnect:
        manager.disconnect(websocket)
````

---

### 2. 프론트엔드 (React)

* 웹소켓 연결을 위한 `WebSocket` 객체 생성
* 메시지 전송 버튼 클릭 시 서버로 전송
* 수신 메시지는 실시간으로 화면에 출력되며 스크롤 동기화 처리됨

```tsx
const socket = new WebSocket(`ws://localhost:3000/ws/${clientId}`);
socket.onmessage = (event) => {
  setMessages((prev) => [...prev, event.data]);
};
```

---

## 🚀 실행 방법

### 1. 프로젝트 클론

```bash
git clone https://github.com/gogumalatte/fastapi-websocket-chat.git
cd 레포명
```

### 2. Docker로 실행

```bash
# 백엔드
docker build -t websocket-chat-backend ./backend
docker run -p 3000:22 websocket-chat-backend

# 프론트엔드
cd frontend
npm install
npm run dev
```

### 3. Ngrok로 외부 접속 (선택)

```bash
ngrok http 22
```

---

## 📁 디렉토리 구조

```
.
├── backend/
│   └── app          # FastAPI 서버 및 WebSocket 핸들링
├── frontend/
│   ├── src/
│   │   ├── App.js      # 메시지 전송/수신 UI
│   │   └── ...
├── Dockerfile
└── README.md
```

---

## 📌 참고 사항

* **FastAPI 서버는 기본적으로 `localhost:3000`에서 실행**됩니다.
* **프론트엔드에서 연결 시 포트 설정을 주의해주세요** (`ws://localhost:3000`)
* 실제 운영 환경에서는 **Redis, DB 연동, 사용자 인증** 기능 등을 추가해 확장할 수 있습니다.

---

## 📜 라이선스

MIT License
