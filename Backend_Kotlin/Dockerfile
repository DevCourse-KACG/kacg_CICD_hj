# ========================
# 1. Build Stage
# ========================
# Kotlin, Java 21, Gradle 프로젝트를 빌드하기 위한 스테이지
FROM gradle:jdk-21-and-23-graal-jammy AS builder

# 작업 디렉토리 설정
WORKDIR /workspace/app

# 소스 코드와 Gradle 래퍼 복사
COPY build.gradle.kts .
COPY settings.gradle.kts .
COPY gradlew .
COPY gradle gradle

# radlew 스크립트에 실행 권한 부여
RUN chmod +x ./gradlew

# Gradle 종속성을 먼저 다운로드
RUN ./gradlew dependencies --no-daemon

# 소스 코드 복사
COPY src src

# 애플리케이션 빌드
RUN ./gradlew build --no-daemon -x test

# ========================
# 2. Runtime Stage
# ========================
FROM eclipse-temurin:21-jre-jammy

# 작업 디렉토리 설정
WORKDIR /app

# 첫 번째 스테이지에서 빌드된 JAR 파일 복사
COPY --from=builder /workspace/app/build/libs/*.jar app.jar

# 포트 설정 (Spring Boot 기본 8080)
EXPOSE 8080

# 실행할 JAR 파일 지정
ENTRYPOINT ["java", "-Dspring.profiles.active=prod", "-jar", "app.jar"]