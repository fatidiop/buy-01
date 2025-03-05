FROM maven:3-openjdk-17-slim

WORKDIR /app

COPY pom.xml ./
COPY .mvn .mvn
RUN mvn dependency:go-offline -B

COPY src ./src

#CMD ["mvn"]

CMD ["mvn", "spring-boot:run"]

#FROM maven:3-openjdk-17-slim AS dependencies
#WORKDIR /app
#COPY pom.xml ./
#COPY .mvn .mvn
#RUN mvn dependency:go-offline -B
#
#FROM dependencies AS build
#COPY src ./src
#RUN mvn package -DskipTests
#
#FROM openjdk:17-slim
#WORKDIR /app
#COPY --from=build /app/target/my-app.jar ./my-app.jar
#CMD ["java", "-jar", "my-app.jar"]

