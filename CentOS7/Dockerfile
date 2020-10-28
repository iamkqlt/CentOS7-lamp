FROM scratch
ADD centos-7-x86_64-docker.tar.xz /
COPY mysql_init /tmp/mysql_init
COPY Centos-7.repo /tmp/Centos-7.repo

WORKDIR /tmp

LABEL \
    org.label-schema.schema-version="1.0" \
    org.label-schema.name="CentOS Base Image" \
    org.label-schema.vendor="CentOS" \
    org.label-schema.license="GPLv2" \
    org.label-schema.build-date="20200809" \
    org.opencontainers.image.title="CentOS Base Image" \
    org.opencontainers.image.vendor="CentOS" \
    org.opencontainers.image.licenses="GPL-2.0-only" \
    org.opencontainers.image.created="2020-08-09 00:00:00+01:00"
    
VOLUME ["/var/www/html", "/var/lib/mysql"]
    
RUN mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak && \
	cp /tmp/Centos-7.repo /etc/yum.repos.d/CentOS-Base.repo && \
    sed -i -e '/mirrors.cloud.aliyuncs.com/d' -e '/mirrors.aliyuncs.com/d' /etc/yum.repos.d/CentOS-Base.repo && \
    yum install -y php php-gd php-mysql httpd mariadb mariadb-server sudo && \
    sudo systemctl start httpd && \
    sudo systemctl enable httpd && \
    sudo systemctl start mariadb && \
    sudo systemctl enable mariadb && \
    find / | grep php.ini$ | xargs sed -i 's/^request_order = ".*"$/request_order = "CGP"/g' && \
    chown -R apache:apache /var/www && \
	chmod -R 0755 /var/www/html && \
	chmod 0444 /var/www
	
RUN	mysql -uroot < /tmp/mysql_init && \
	rm -f /tmp/mysql_init && \
	systemctl restart mariadb
	
WORKDIR /home
    
EXPOSE 80

CMD ["/bin/bash"]
