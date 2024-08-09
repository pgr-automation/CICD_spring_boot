# Use Maven to build the application
FROM maven AS build

# Set the working directory
WORKDIR /app

# Copy Maven project file and download dependencies
COPY ./pom.xml .
RUN mvn dependency:go-offline

# Copy source code and build the application
COPY ./src ./src
RUN mvn clean package

# Use OpenJDK to run the application
FROM openjdk:17-jdk-slim

# Set the working directory
WORKDIR /app

# Copy the built JAR file from the build stage
COPY --from=build /app/target/demo-0.0.1-SNAPSHOT.jar app.jar


# Expose the port the app runs on
EXPOSE 8080

# Define the command to run the JAR file
ENTRYPOINT ["java", "-jar", "app.jar"]
