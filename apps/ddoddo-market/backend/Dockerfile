### 1단계: Gradle로 빌드
FROM gradle:8.5-jdk17 AS builder
WORKDIR /app
COPY . .
RUN chmod +x gradlew && ./gradlew bootJar

### 2단계: JAR만 복사해서 실행
FROM openjdk:17-jdk-slim
COPY --from=builder /app/build/libs/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
