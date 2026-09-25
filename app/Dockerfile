FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
docker build -t week9-devops-app:v1 .
docker run -d --name week9-app -p 8081:80 week9-devops-app:v1

