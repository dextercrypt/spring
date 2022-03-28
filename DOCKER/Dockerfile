FROM openjdk:8
COPY spring-boot-with-prometheus-0.1.0.jar /spring-boot-with-prometheus-0.1.0.jar
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]


