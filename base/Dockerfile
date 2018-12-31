FROM centos:6.10
MAINTAINER takaya030

# update yum, add external repos
RUN yum update -y \
	&& yum install -y http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm \
	&& sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/epel.repo \
	&& yum install -y http://rpms.famillecollet.com/enterprise/remi-release-6.rpm \
	&& sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/remi.repo

# install packages
RUN yum install -y sudo which unzip graphviz graphviz-gd \
	&& yum install --enablerepo=epel -y libmcrypt libwebp \
	&& yum install --enablerepo=remi -y gd-last \
	&& yum install --enablerepo=remi-php55 -y php php-devel php-gd php-mbstring php-mcrypt php-mysqlnd php-xml php-opcache \
	&& yum install --enablerepo=remi,remi-php55 -y php-pecl-memcached php-pecl-xhprof \
	&& yum clean all

# modify /etc/php.ini
RUN sed -i -e "s/;date.timezone *=.*$/date.timezone = Asia\/Tokyo/" /etc/php.ini \
	&& sed -i -e "s/^upload_max_filesize *=.*$/upload_max_filesize = 32M/" /etc/php.ini \
	&& sed -i -e "s/^max_file_uploads *=.*$/max_file_uploads = 128/" /etc/php.ini \
	&& sed -i -e "s/^post_max_size *=.*$/post_max_size = 32M/" /etc/php.ini

# timezone
RUN ln -sf /usr/share/zoneinfo/Japan /etc/localtime

CMD ["true"]
