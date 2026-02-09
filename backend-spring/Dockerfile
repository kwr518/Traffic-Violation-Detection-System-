FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
# jar 파일 위치 주의 (미리 빌드해둬야 함)
COPY build/libs/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]