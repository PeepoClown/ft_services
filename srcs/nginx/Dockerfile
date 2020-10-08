FROM alpine:latest
LABEL maintainer="wupdegra@student.21-school.ru"

# update & install packages
RUN apk update --no-cache && \
	apk upgrade --no-cache && \
	apk add --no-cache nginx \
					   openssl \
					   openssh \
					   supervisor

# configure nginx
RUN adduser -D -g 'www' www && \
	mkdir -p /www /run/nginx && \
	chown -R www:www /www && \
	chown -R www:www /var/lib/nginx

# copy sources
COPY srcs/nginx.conf /etc/nginx/nginx.conf
COPY srcs/index.html /www/index.html
COPY srcs/supervisord.conf /etc/supervisord.conf

# FCKNG SSH
RUN adduser -D ssh_user && echo 'ssh_user:psswd' | chpasswd
RUN ssh-keygen -A
COPY srcs/sshd_config /etc/ssh/sshd_config

# generate ssl cert
RUN openssl req -x509 -nodes -days 90 -newkey rsa:2048 \
	-subj '/C=RU/ST=Moscow/L=Moscow/O=ecole42/OU=21School/CN=wupdegra' \
	-keyout /etc/ssl/certs/localhost.key \
	-out /etc/ssl/certs/localhost.crt

EXPOSE 80 443 22

# run container
COPY srcs/setup.sh /var/setup.sh
RUN chmod 755 /var/setup.sh

ENTRYPOINT [ "/var/setup.sh" ]