# backend-ai/Dockerfile

FROM python:3.10-slim
WORKDIR /app

# 시스템 라이브러리 설치 (OpenCV 등)
RUN apt-get update && apt-get install -y \
    libgl1 \
    libglib2.0-0 \
    libsm6 \
    libxext6 \
    libxrender1 \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 현재 폴더의 모든 파일(app 폴더 포함)을 컨테이너의 /app으로 복사
COPY . .

EXPOSE 8000

# ★ 실행 경로를 app.main:app으로 변경 ★
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]