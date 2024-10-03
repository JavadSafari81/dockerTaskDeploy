FROM ubuntu:latest

RUN apt-get update && \
    apt-get install -y net-tools vim nginx

COPY default.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
