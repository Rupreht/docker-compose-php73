FROM nginx:alpine

ADD nginx.conf /etc/nginx/nginx.conf
ADD mime.types /etc/nginx/mime.types

RUN mkdir -p /application/static
RUN mkdir -p /application/media

#RUN chown -R nginx:nginx /media
#RUN chown -R nginx:nginx /static

WORKDIR /application
