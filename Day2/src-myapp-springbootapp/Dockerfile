# sudo java -jar myapp.jar > /dev/null 2> /dev/null < /dev/null &
# JAVA ENV
# OPEN 8080
# RUNNING OF JAR FILE
# FROM ibmjava:jre

# FROM openjdk:17

# EXPOSE 8080

# RUN mkdir /opt/app

# COPY ./target/spring-boot-complete-0.0.1-SNAPSHOT.jar /opt/app/myapp.jar

# CMD ["java", "-jar", "/opt/app/myapp.jar"]

FROM ubuntu:20.04

WORKDIR /myapp

# RUN apt-get update -y && apt install default-jre -y

RUN apt-get update -y && apt install openjdk-17-jdk -y

COPY target/spring-boot-complete-0.0.1-SNAPSHOT.jar myapp.jar

EXPOSE 8080

CMD [ "java","-jar","myapp.jar" ]

