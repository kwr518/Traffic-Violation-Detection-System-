🚦 Road Guardian (도로 위법 차량 자동 탐지 및 신고 플랫폼)"교통 위반 신고, 이제 AI가 대신합니다."**Edge Device(라즈베리파이)**에서 위반 차량을 탐지하고, Cloud Server에서 정밀 분석 후, RPA를 통해 관할 기관(안전신문고)에 자동으로 신고하는 One-Stop 파이프라인 시스템입니다.📋 1. 프로젝트 개요 (Overview)📅 개발 기간2026. 01. 16 ~ 2026. 02. 13 (총 4주)🎯 기획 의도기존의 교통 위반 신고 절차는 **[영상 촬영 → PC 이동 → 편집 → 신고 사이트 접속 → 내용 작성 → 제출]**이라는 복잡한 과정을 거쳐야 했으며, 약 10분 이상의 시간이 소요되었습니다.Road Guardian은 이 모든 과정을 **AI와 자동화 기술(RPA)**로 해결하여 신고 소요 시간을 **2분 이내(80% 단축)**로 줄이고자 기획되었습니다.🏗 2. 시스템 아키텍처 (System Architecture)이 프로젝트는 MSA(Microservices Architecture) 지향 구조로 설계되었으며, Docker Compose를 통해 모든 서비스가 컨테이너 환경에서 유기적으로 동작합니다.🔄 데이터 파이프라인 흐름Edge (Raspberry Pi): 도로 주행 영상 촬영 및 1차 필터링AI Server (FastAPI): YOLOv8 + LSTM 기반 위반 차량 정밀 분석 & OCR 번호판 인식Main Server (Spring Boot): 사용자 관리, 신고 데이터 처리, DB 트랜잭션 관리RPA (Selenium): 분석된 데이터를 바탕으로 안전신문고 자동 신고 접수Frontend (React/Next.js): 실시간 대시보드, 내 기기 관리, 신고 내역 조회코드 스니펫graph TD
    User[사용자/Edge Device] -->|Upload Video| Frontend
    Frontend -->|API Request| Spring[Spring Boot (Main Server)]
    Spring -->|Analysis Request| FastAPI[FastAPI (AI Server)]
    FastAPI -->|Object Detection| YOLO[YOLOv8 Model]
    FastAPI -->|Auto Report| Selenium[RPA Bot]
    Selenium -->|Submit| Gov[안전신문고 (External)]
    Spring -->|Save Data| DB[(MariaDB)]
    FastAPI -->|Result Sync| Spring
🛠 3. 기술 스택 (Tech Stack)FrontendFramework: React.js / Next.jsState Management: Context APIStyling: CSS Modules / Styled-componentsHttp Client: Axios (Interceptors 적용)Backend (Main)Language: Java 17Framework: Spring Boot 3.4Database: MariaDB 10.11ORM: JPA (Hibernate), MyBatisAuth: OAuth 2.0 (Kakao, Google)Backend (AI & RPA)Language: Python 3.10Framework: FastAPIAI Models:Detection: YOLOv8 (Vehicle, License Plate)Action Recognition: LSTM (끼어들기, 위협 운전 판별)OCR: EasyOCR / PaddleOCRLLM: RAG 기반 법률 상담 챗봇 (LangChain)Automation: Selenium WebDriver (Headless Chrome)Infra & DevOpsVirtualization: Docker, Docker ComposeDeployment: AWS EC2 (예정)Tool: Git, GitHub, Swagger UI📂 4. 프로젝트 구조 (Directory Structure)BashTraffic-Violation-System/
├── frontend/                # React/Next.js 클라이언트 소스
│   ├── src/components/      # 재사용 가능한 UI 컴포넌트
│   ├── src/pages/           # 라우팅 페이지 (Support.jsx 등)
│   ├── Dockerfile           # 프론트엔드 이미지 빌드 설정
│   └── ...
├── backend-spring/          # Spring Boot 메인 서버
│   ├── src/main/java/       # Controller, Service, DTO, Entity
│   ├── src/main/resources/  # application.properties (DB 설정)
│   ├── Dockerfile           # 백엔드 이미지 빌드 설정
│   └── ...
├── backend-ai/              # FastAPI AI 서버
│   ├── app/routers/         # AI 분석 및 인증(Auth) 라우터
│   ├── app/models/          # YOLO 가중치 파일 (.pt)
│   ├── Dockerfile           # AI 서버 이미지 빌드 설정 (OpenCV, TF 포함)
│   └── ...
└── docker-compose.yml       # ⭐️ 전체 시스템 오케스트레이션 설정
🚀 5. 주요 기능 (Key Features)1️⃣ AI 기반 위법 차량 정밀 탐지단순 이미지 인식이 아닌 영상 기반 분석.YOLOv8로 차량과 번호판을 실시간 추적하고, LSTM 알고리즘을 통해 "차선 변경 위반", "신호 위반" 등의 행동(Action)을 인식.2️⃣ 원스톱 자동 신고 (RPA Automation)사용자가 일일이 입력하던 [위반 일시, 장소, 차량 번호, 위반 내용]을 AI가 추출.Selenium 봇이 안전신문고 웹사이트에 접속하여 신고서를 자동으로 작성 및 제출.3️⃣ 소셜 로그인 및 기기 연동 (IoT)Kakao / Google OAuth 2.0 지원으로 간편 가입.마이페이지에서 **라즈베리파이 시리얼 넘버(UUID)**를 등록하면, 엣지 디바이스와 계정이 자동 연동되어 영상이 클라우드로 동기화됨.4️⃣ 하이브리드 인프라 구축Localhost(개발), Docker Internal(통신), AWS(배포) 환경을 고려한 유연한 네트워크 설계.프론트엔드와 백엔드 간 CORS 이슈 완벽 해결.🔥 6. 트러블슈팅 (Troubleshooting)이슈 1: Docker 네트워크 격리 및 통신 에러문제: Docker 컨테이너 내부에서는 localhost가 자기 자신을 가리키기 때문에, React(브라우저)에서 http://backend:8080을 호출하면 연결 거부(Connection Refused) 발생.해결:Server-to-Server 통신: Docker Service Name (http://backend:8080, http://fastapi:8000) 사용.Client-to-Server 통신: Host Machine의 포트를 개방하고, 클라이언트에서는 http://localhost:8080을 사용하도록 이원화 전략 수립.이슈 2: 이종 플랫폼 간 ID 데이터 타입 불일치 (Type Mismatch)문제: 레거시 DB는 INT형 PK를 사용했으나, 소셜 로그인(Kakao/Google)은 String형 ID(kakao_123...)를 반환하여 400 Bad Request 및 DB 저장 실패.해결:DB 스키마의 history_id 컬럼을 VARCHAR로 마이그레이션.Spring Boot의 DTO 및 JPA Entity 매핑을 String 타입으로 리팩토링하여 확장성 확보.이슈 3: AI 라이브러리 Docker 빌드 최적화문제: TensorFlow, OpenCV 등 무거운 라이브러리 설치 시 빌드 시간 과다 소요 및 의존성 충돌.해결:Docker Layer Caching을 적극 활용.libgl1-mesa-glx 등 OS 레벨 의존성을 Dockerfile에 명시하여 런타임 에러 해결.💻 7. 설치 및 실행 가이드 (Getting Started)Docker가 설치되어 있다면 명령어 한 줄로 실행 가능합니다.프로젝트 클론Bashgit clone https://github.com/your-username/Traffic-Violation-System.git
cd Traffic-Violation-System
환경 변수 설정 (.env)프로젝트 루트에 .env 파일을 생성하고 Kakao/Google API 키를 입력하세요.코드 스니펫KAKAO_CLIENT_ID=your_key_here
MYSQL_ROOT_PASSWORD=your_password
Docker Compose 실행 (빌드 및 실행)Bashdocker-compose up --build
초기 빌드 시 AI 모델 다운로드로 인해 약 5~10분 소요될 수 있습니다.접속 주소Frontend: http://localhost:3000Spring Boot API: http://localhost:8080FastAPI Docs: http://localhost:8000/docs👨‍💻 8. 팀원 및 역할 (Team)이름역할담당 업무OOOTeam Leader / Full Stack프로젝트 총괄, 아키텍처 설계, Docker 인프라 구축OOOBackend / AIFastAPI 서버 구현, YOLO/LSTM 모델 학습 및 최적화OOOBackend / RPASpring Boot API 개발, Selenium 자동 신고 봇 구현OOOFrontendReact UI/UX 디자인, 대시보드 및 마이페이지 구현OOOEmbedded / IoT라즈베리파이 영상 처리 및 통신 모듈 개발
