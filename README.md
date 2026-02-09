# Traffic-Violation-Detection-System-
AI 기반 도로 위법 차량 실시간 탐지 및 자동 신고 플랫폼 (Spring Boot &amp; FastAPI 분산 구조)

graph TD
    User[사용자/Edge Device] -->|Upload Video| Frontend
    Frontend -->|API Request| Spring[Spring Boot (Main Server)]
    Spring -->|Analysis Request| FastAPI[FastAPI (AI Server)]
    FastAPI -->|Object Detection| YOLO[YOLOv8 Model]
    FastAPI -->|Auto Report| Selenium[RPA Bot]
    Selenium -->|Submit| Gov[안전신문고 (External)]
    Spring -->|Save Data| DB[(MariaDB)]
    FastAPI -->|Result Sync| Spring
