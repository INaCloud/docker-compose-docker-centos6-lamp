#
# base image
#
FROM centos:6.10 AS build-stage-base
MAINTAINER takaya030

# update yum, add external repos
RUN yum update -y \
	&& yum install -y http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm \
	&& sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/epel.repo \
	&& yum install -y http://rpms.famillecollet.com/enterprise/remi-release-6.rpm \
	&& sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/remi.repo

# install packages
RUN yum install -y sudo which unzip \
	&& yum install --enablerepo=epel -y libmcrypt libwebp \
	&& yum install --enablerepo=remi -y gd-last \
	&& yum install --enablerepo=remi-php55 -y php php-devel php-gd php-mbstring php-mcrypt php-mysqlnd php-xml php-opcache \
	&& yum install --enablerepo=remi,remi-php55 -y php-pecl-memcached php-pecl-xdebug \
	&& yum clean all

# php custom config
COPY ./z-myconfig.ini /etc/php.d/z-myconfig.ini

# timezone
RUN ln -sf /usr/share/zoneinfo/Japan /etc/localtime

CMD ["true"]


#
# web image
#
FROM build-stage-base AS build-stage-web

# install packages
RUN yum install -y httpd httpd-tools \
	&& yum clean all

ENV WEBAPP_ROOT /webapp

COPY ./httpd.conf /etc/httpd/conf/httpd.conf
COPY ./index.html /webapp/public/index.html
COPY ./phpinfo.php /webapp/public/phpinfo.php

EXPOSE 80

CMD ["/usr/sbin/httpd","-DFOREGROUND"]
