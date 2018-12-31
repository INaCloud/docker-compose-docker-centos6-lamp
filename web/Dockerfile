FROM takaya030/php:5.5
MAINTAINER takaya030

# install packages
RUN yum install -y httpd httpd-tools \
	&& yum clean all

ENV WEBAPP_ROOT /webapp

ADD ./httpd.conf /etc/httpd/conf/httpd.conf
ADD ./index.html /webapp/public/index.html
ADD ./phpinfo.php /webapp/public/phpinfo.php

EXPOSE 80

CMD ["/usr/sbin/httpd","-DFOREGROUND"]
