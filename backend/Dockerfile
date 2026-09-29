# ---- Stage 1: build the JAR ----
FROM maven:3.9.6-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline -B
COPY src ./src
RUN mvn clean package -DskipTests

# ---- Stage 2: run the JAR ----
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar

# Render injects PORT at runtime; application.properties reads it via ${PORT:8080}.
# This is only a local fallback.
ENV PORT=8080
EXPOSE 8080

# Render's free instance has 512 MB. The JVM defaults to a 25% heap (~128 MB),
# which is too small for Spring Boot to boot. Give it 75% and use SerialGC so a
# 0.1-CPU instance isn't fighting multiple GC threads for memory.
ENTRYPOINT ["java", "-XX:MaxRAMPercentage=75.0", "-XX:+UseSerialGC", "-jar", "app.jar"]
