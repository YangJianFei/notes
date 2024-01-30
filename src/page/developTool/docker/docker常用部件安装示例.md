# 挂载目录
/root/docker/nginx/
/root/docker/mysql/


## docker安装nginx

运行与挂载：/home/heifahaizei/docker/nginx

sudo docker run --name nginx -p 80:80 -p 443:443 --restart=always 
-v ./conf/nginx.conf:/etc/nginx/nginx.conf 
-v ./conf.d:/etc/nginx/conf.d 
-v ./html:/usr/share/nginx/html 
-v ./logs:/var/log/nginx 
-d nginx

sudo docker run --name nginx -p 80:80 -p 443:443 --restart=always -v ./conf/nginx.conf:/etc/nginx/nginx.conf -v ./conf.d:/etc/nginx/conf.d -v ./html:/usr/share/nginx/html -v ./logs:/var/log/nginx -d nginx


## docker安装mysql5.7
 docker run --name mysql -p 3306:3306 --restart unless-stopped -v ./log:/var/log/mysql -v ./data:/var/lib/mysql -v ./conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=Yongtai123321# -d mysql:5.7

## docker安装node
1：第一步实例化镜像
https://juejin.cn/post/7178673705462169655
docker run -dit --name node node:16

2：构建dockerfile
FROM node:16

WORKDIR /root/battery-check-node

EXPOSE 3000

CMD ["npm","start"]



3：创建新镜像
docker build -t battery-node .

4：实例化新镜像
docker run --name battery-node -p 3000:3000 --restart=always -d battery-node
docker run --name battery-node -p 3000:3000 --restart=always -v /root/docker/battery-check-node:/root/battery-check-node -d battery-node