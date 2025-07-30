# 1단계: Gradle 이미지로 빌드
FROM gradle:8.5-jdk17 AS builder
WORKDIR /app

# 소스 전체 복사
COPY . .

# Gradle CLI로 BootJar 생성 (–no-daemon 옵션으로 데몬 없이 실행)
RUN gradle bootJar --no-daemon

# 2단계: JRE만 담긴 슬림 이미지에 JAR 복사
FROM openjdk:17-jdk-slim
WORKDIR /app

# 빌드 결과 JAR 파일(app/build/libs/*.jar)을 app.jar로 복사
COPY --from=builder /app/build/libs/*.jar app.jar

# 컨테이너 시작 시 Spring Boot 앱 구동
ENTRYPOINT ["java", "-jar", "app.jar"]
