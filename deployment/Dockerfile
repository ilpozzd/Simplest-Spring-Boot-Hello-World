FROM eclipse-temurin:11-alpine

RUN addgroup -S spring && adduser -S spring -G spring 
USER spring:spring  
WORKDIR /home/spring

ARG WAR=
COPY ${WAR} app.war

ENTRYPOINT ["java", "-jar", "app.war"]