FROM scratch
ADD centos-7-x86_64-docker.tar.xz /

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
    
RUN curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo && \
    sed -i -e '/mirrors.cloud.aliyuncs.com/d' -e '/mirrors.aliyuncs.com/d' /etc/yum.repos.d/CentOS-Base.repo && \
    yum upgrade -y && \
    yum install -y php php-gd php-mysql apache2 mariadb mariadb-server && \
    sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config && \
    systemctl start firewalld && \
    systemctl enable firewalld && \
    firewall-cmd --zone=public --add-port=80/tcp --permanent && \
    systemctl start httpd && \
    systemctl enable httpd && \
    systemctl start mariadb && \
    systemctl enable mariadb && \
    find / | grep php.ini$ | xargs sed -i 's/^request_order = ".*"$/request_order = "CGP"/g' && \
    chown -R apache:apache /var/www && \
	chmod -R 0755 /var/www/html && \
	chmod 0444 /var/www
	
RUN	cat > /tmp/mysql_init <<EOF
	use mysql;
	delete from mysql.user where user='root' and host!='localhost';
	delete from mysql.user where user='anonymous';
	update mysql.user set password=password('RootPassword') where user='root';
	create database dede_data;
	grant all privileges on dede_data.*  to 'dedeAdmin'@'%' identified by 'AdminPassword';
	drop database test;
	flush privileges;
	\EOF && \
	mysql -uroot < /tmp/mysql_init && \
	rm -f /tmp/mysql_init && \
	systemctl restart mariadb
    
EXPOSE 80

CMD ["/bin/bash"]
