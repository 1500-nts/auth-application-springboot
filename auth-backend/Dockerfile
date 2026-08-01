#Build stage
FROM maven:3.9.6-eclipse-temurin-21 AS build

# Set working directory
WORKDIR /app

# Copy pom.xml and download dependencies (cached layer)
COPY pom.xml .
RUN mvn dependency:go-offline

# Copy source code
COPY src ./src

# Build the application
RUN mvn clean package -DskipTests




#Run stage

FROM eclipse-temurin:21-jre

# Set working directory
WORKDIR /app

# Copy jar from build stage
COPY --from=build /app/target/*.jar app.jar

# Render injects PORT at runtime (default 10000); this is just documentation for the image
EXPOSE 8080

# Cap JVM heap to fit Render's free 512MB instance and avoid OOM kills.
# SPRING_PROFILES_ACTIVE defaults to prod but can be overridden by Render's env vars.
ENV JAVA_OPTS="-XX:MaxRAMPercentage=75 -XX:+UseSerialGC" \
    SPRING_PROFILES_ACTIVE=prod

# Run the Spring Boot app
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]