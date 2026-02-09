# 🚦 Road Guardian (도로 위법 차량 자동 탐지 및 원스톱 신고 플랫폼)

![Project Status](https://img.shields.io/badge/Status-Active-brightgreen) ![License](https://img.shields.io/badge/License-MIT-blue) ![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white) ![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.4-6DB33F?logo=springboot&logoColor=white) ![FastAPI](https://img.shields.io/badge/FastAPI-0.95-009688?logo=fastapi&logoColor=white) ![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)

> **"교통 위반 신고, 이제 AI가 대신합니다."**
>
> **Edge Device(라즈베리파이)**에서 위반 차량을 실시간 탐지하고, **Cloud Server**에서 정밀 분석 후, **RPA**를 통해 관할 기관(안전신문고)에 **자동으로 신고**하는 One-Stop 파이프라인 시스템입니다.

---

## 📋 1. 프로젝트 개요 (Overview)

### 📅 개발 기간
**2026. 01. 16 ~ 2026. 02. 13 (총 4주)**

### 🎯 기획 의도 및 해결 과제
기존의 교통 위반 신고 절차는 **[영상 촬영 → PC 이동 → 편집 → 신고 사이트 접속 → 내용 작성 → 제출]**이라는 복잡한 과정을 거쳐야 했으며, 건당 약 10분 이상의 시간이 소요되어 사용자 이탈률이 높았습니다.

**Road Guardian**은 이 모든 과정을 **AI와 자동화 기술(RPA)**로 해결하여 신고 소요 시간을 **2분 이내(80% 단축)**로 줄이고, 시민들의 자발적인 교통 법규 준수를 유도하고자 기획되었습니다.

---

## 🏗 2. 시스템 아키텍처 (System Architecture)

본 프로젝트는 **MSA(Microservices Architecture)**를 지향하며, **Docker Compose**를 통해 Frontend, Backend, AI Server, Database가 유기적으로 연동됩니다.

### 🔄 데이터 처리 파이프라인
1.  **Edge (Raspberry Pi):** 도로 주행 영상 촬영 및 1차 필터링 (번호판 식별 가능 영상 선별)
2.  **AI Server (FastAPI):** YOLOv8 + LSTM 기반 위반 차량 정밀 분석 & OCR 번호판 텍스트 추출
3.  **Main Server (Spring Boot):** 사용자/기기 관리, 신고 데이터 트랜잭션 처리, OAuth2 인증
4.  **RPA (Selenium):** 분석된 데이터를 바탕으로 안전신문고 웹사이트 자동 신고 접수
5.  **Frontend (React):** 실시간 대시보드, 내 기기(Edge) 관리, 신고 내역 조회

```mermaid
graph TD
    User[사용자/Edge Device] -->|Upload Video| Frontend
    Frontend -->|API Request| Spring[Spring Boot (Main Server)]
    Spring -->|Analysis Request| FastAPI[FastAPI (AI Server)]
    FastAPI -->|Object Detection| YOLO[YOLOv8 Model]
    FastAPI -->|Auto Report| Selenium[RPA Bot]
    Selenium -->|Submit| Gov[안전신문고 (External)]
    Spring -->|Save Data| DB[(MariaDB)]
    FastAPI -->|Result Sync| Spring
```

---

## 🛠 3. 기술 스택 (Tech Stack)

| 구분 | 기술 스택 (Stack) | 활용 내용 |
| :--- | :--- | :--- |
| **Frontend** | **React.js, Next.js** | 사용자 인터페이스 및 대시보드 구현 |
| | **Axios, Context API** | 비동기 데이터 통신 및 전역 상태(로그인) 관리 |
| **Backend (Main)** | **Java 17, Spring Boot 3.4** | REST API 서버, 비즈니스 로직, DB 관리 |
| | **JPA (Hibernate), MyBatis** | 복잡한 쿼리 및 객체 지향 데이터 매핑 |
| **Backend (AI)** | **Python 3.10, FastAPI** | AI 모델 서빙 및 비동기 작업 처리 |
| | **Selenium WebDriver** | 안전신문고 자동 신고 매크로(RPA) 구현 |
| **AI / ML** | **YOLOv8, PyTorch** | 차량 및 번호판 객체 탐지 (Object Detection) |
| | **LSTM / RAG (LangChain)** | 위반 상황 맥락 분석 및 법률 질의응답 |
| **Infra & DevOps** | **Docker, Docker Compose** | 컨테이너 기반 배포 및 네트워크 오케스트레이션 |
| | **MariaDB 10.11** | 관계형 데이터베이스 (RDBMS) |
| **Auth** | **OAuth 2.0 (Kakao, Google)** | 소셜 로그인 구현 (보안 강화) |

---

## 📂 4. 프로젝트 구조 (Directory Structure)

```bash
Traffic-Violation-System/
├── frontend/                # React 클라이언트 소스 (Next.js)
│   ├── src/components/      # UI 컴포넌트
│   ├── src/pages/           # 라우팅 페이지 (Support.jsx, Login.jsx 등)
│   ├── Dockerfile           # 프론트엔드 빌드 설정
│   └── ...
├── backend-spring/          # Spring Boot 메인 서버
│   ├── src/main/java/       # Controller, Service, DTO, Entity
│   ├── src/main/resources/  # application.properties (DB, JPA 설정)
│   ├── Dockerfile           # 백엔드 빌드 설정 (JDK 17)
│   └── ...
├── backend-ai/              # FastAPI AI 서버
│   ├── app/routers/         # AI 분석 및 인증(Auth) 라우터
│   ├── app/models/          # YOLO 가중치 파일 (.pt) 및 학습 모델
│   ├── Dockerfile           # AI 서버 빌드 설정 (OpenCV, TF 포함)
│   └── ...
├── docker-compose.yml       # ⭐️ 전체 시스템 오케스트레이션 설정 파일
└── README.md                # 프로젝트 문서
```

---

## 🚀 5. 주요 기능 (Key Features)

### 1️⃣ AI 기반 위법 차량 정밀 탐지
* 단순 이미지 인식이 아닌 **영상 기반 분석**.
* **YOLOv8**로 차량과 번호판을 실시간 추적하고, **LSTM** 알고리즘을 통해 "차선 변경 위반(실선 침범)", "신호 위반" 등의 **행동(Action)을 인식**.

### 2️⃣ 원스톱 자동 신고 (RPA Automation)
* 사용자가 일일이 입력하던 [위반 일시, 장소, 차량 번호, 위반 내용]을 AI가 영상 메타데이터에서 자동 추출.
* **Selenium 봇**이 실제 안전신문고 웹사이트에 접속하여 **신고서를 자동으로 작성 및 제출**.

### 3️⃣ 소셜 로그인 및 기기 연동 (IoT)
* **Kakao / Google OAuth 2.0** 지원으로 간편 가입 및 로그인.
* 마이페이지에서 **라즈베리파이 시리얼 넘버(UUID)**를 등록하면, 엣지 디바이스와 사용자 계정이 자동 연동되어 촬영된 영상이 클라우드로 즉시 동기화됨.

### 4️⃣ 하이브리드 인프라 구축
* **Docker Network**를 활용한 컨테이너 간 내부 통신 최적화.
* 프론트엔드(Browser)와 백엔드(Container) 간의 **CORS 이슈 완벽 해결**.

---

## 🔥 6. 트러블슈팅 (Troubleshooting) - 핵심 문제 해결 경험

### 이슈 1: Docker 네트워크 격리 및 통신 에러
* **문제:** Docker 컨테이너 내부에서는 `localhost`가 자기 자신을 가리키기 때문에, React(브라우저)에서 `http://backend:8080`을 호출하면 연결 거부(Connection Refused) 발생.
* **해결:**
    * **Server-to-Server 통신:** Docker Service Name (`http://backend:8080`, `http://fastapi:8000`) 사용.
    * **Client-to-Server 통신:** Host Machine의 포트를 개방하고, 클라이언트(React)에서는 `http://localhost:8080`을 사용하도록 이원화 전략 수립.

### 이슈 2: 이종 플랫폼 간 ID 데이터 타입 불일치 (Type Mismatch)
* **문제:** 레거시 DB 설계는 `INT`형 PK를 사용했으나, 소셜 로그인(Kakao/Google)은 `String`형 ID(`kakao_123...`)를 반환하여 `400 Bad Request` 및 DB 저장 실패.
* **해결:**
    * DB 스키마의 `history_id` 및 참조 FK 컬럼을 `VARCHAR`로 마이그레이션.
    * Spring Boot의 DTO 및 JPA Entity 매핑을 `String` 타입으로 리팩토링하여 확장성 확보.

### 이슈 3: AI 라이브러리 Docker 빌드 최적화
* **문제:** `TensorFlow`, `OpenCV` 등 무거운 라이브러리 설치 시 빌드 시간 과다 소요 및 시스템 의존성 충돌(`libgl1` 누락 등).
* **해결:**
    * Docker Layer Caching을 적극 활용하여 재빌드 속도 개선.
    * `libgl1-mesa-glx` 등 OS 레벨 필수 패키지를 `Dockerfile`에 명시하여 런타임 에러 해결.

---

## 💻 7. 설치 및 실행 가이드 (Getting Started)

**Docker가 설치되어 있다면 명령어 한 줄로 전체 시스템 실행이 가능합니다.**

1. **프로젝트 클론**
   ```bash
   git clone [https://github.com/your-username/Traffic-Violation-System.git](https://github.com/your-username/Traffic-Violation-System.git)
   cd Traffic-Violation-System
   ```

2. **환경 변수 설정 (.env)**
   * 프로젝트 루트에 `.env` 파일을 생성하고 Kakao/Google API 키를 입력하세요.
   ```env
   KAKAO_CLIENT_ID=your_kakao_key
   KAKAO_CLIENT_SECRET=your_secret_key
   MYSQL_ROOT_PASSWORD=your_db_password
   ```

3. **Docker Compose 실행 (빌드 및 실행)**
   ```bash
   docker-compose up --build
   ```
   * *초기 빌드 시 AI 모델 다운로드로 인해 약 5~10분 소요될 수 있습니다.*

4. **접속 주소**
   * **Frontend:** [http://localhost:3000](http://localhost:3000)
   * **Spring Boot API:** [http://localhost:8080](http://localhost:8080)
   * **FastAPI Docs:** [http://localhost:8000/docs](http://localhost:8000/docs)

---

## 👨‍💻 8. 팀원 및 역할 (Team)

| 이름 | 역할 | 담당 업무 |
|:---:|:---:|:---|
| **본인 이름** | **Full Stack / Infra** | 아키텍처 설계, Docker 인프라 구축, OAuth2 연동, 트러블슈팅 총괄 |
| 팀원1 | Backend / AI | FastAPI 서버 구현, YOLO/LSTM 모델 학습 및 최적화 |
| 팀원2 | Backend / RPA | Spring Boot API 개발, Selenium 자동 신고 봇 구현 |
| 팀원3 | Frontend | React UI/UX 디자인, 대시보드 및 마이페이지 구현 |
| 팀원4 | Embedded / IoT | 라즈베리파이 영상 처리 및 통신 모듈 개발 |
