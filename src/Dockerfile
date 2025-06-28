FROM nginx:alpine

WORKDIR /home/laneryel

RUN apk add --no-cache gcc g++ libc-dev spawn-fcgi fcgi-dev bash

COPY /server/fcgi_hello.c .
RUN gcc fcgi_hello.c -lfcgi -o fcgi_hello.fcgi && \
    chmod +x fcgi_hello.fcgi

COPY nginx/nginx.conf /etc/nginx/nginx.conf

CMD spawn-fcgi -p 8080 ./fcgi_hello.fcgi && nginx -g 'daemon off;'
