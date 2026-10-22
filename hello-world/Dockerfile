FROM tomcat:latest
RUN cp -R  /usr/local/tomcat/webapps.dist/*  /usr/local/tomcat/webapps
RUN rm -rf /usr/local/tomcat/webapps/*
COPY hello-world/webapp/target/*.war /usr/local/tomcat/webapps

