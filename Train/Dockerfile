# 使用官方 MySQL 镜像
FROM mysql:8.0

# 复制初始化脚本
COPY Train.sql /docker-entrypoint-initdb.d/

# 设置环境变量
#ENV MYSQL_ROOT_PASSWORD=rootpassword
#ENV MYSQL_DATABASE=mydatabase

EXPOSE 3306