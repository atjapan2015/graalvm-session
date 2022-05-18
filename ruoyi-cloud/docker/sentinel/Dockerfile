# 基础镜像
FROM atjapan2015/graalvm-ce-java17:22.1.0

# 复制jar文件到路径
COPY ./jar/sentinel-dashboard.jar sentinel-dashboard.jar
# 启动系统服务
ENTRYPOINT ["java","-jar","sentinel-dashboard.jar"]
