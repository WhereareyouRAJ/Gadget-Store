# Step 1: Build the UI
FROM maven:3.9-eclipse-temurin-21-alpine AS build

# Set the Working Directory 
WORKDIR /app  

# Cache Management 
# Copying only the dependecies file first   
COPY pom.xml ./   

RUN mvn dependency:go-offline 

COPY src ./src

RUN mvn clean package -DskipTests

# Step 2: Final Stage
# Using Distroless Image for Java 21 on Debian 12, which only contains the JVM and its dependencies, 
# No shell, No package manager and No terminal
FROM gcr.io/distroless/java21-debian13

WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

# Principle of least privilege
# We run as non-root, a built-in user in Distroless    
USER nonroot:nonroot

ENV JAVA_TOOL_OPTIONS=
ENV SPRING_PROFILES_ACTIVE=prod

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]









