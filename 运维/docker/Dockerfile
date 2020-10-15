###指定java8环境镜像
FROM java:8
MAINTAINER cc
ADD helloworld.jar /helloworld.jar
###声明启动端口号
EXPOSE 10001
###配置容器启动后执行的命令
ENTRYPOINT ["java","-jar","/helloworld.jar"]
