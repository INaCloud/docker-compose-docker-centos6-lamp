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


#
# manage image
#
FROM build-stage-base AS build-stage-manage

# add external repos
# install packages
RUN yum install -y http://dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm \
	&& sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/mysql-community.repo \
	&& yum install -y https://centos6.iuscommunity.org/ius-release.rpm \
	&& sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/ius.repo \
	&& yum install -y openssh-server openssh-clients \
	&& yum install --enablerepo=mysql56-community -y mysql-community-client \
	&& yum install --enablerepo=ius -y git2u \
	&& yum clean all

# composer, phpunit
# for git prompt
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
	&& curl -L https://phar.phpunit.de/phpunit-old.phar > /usr/local/bin/phpunit \
	&& chmod +x /usr/local/bin/phpunit \
	&& curl -L https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash > /etc/skel/.git-completion.bash \
	&& curl -L https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh > /etc/skel/.git-prompt.sh 
COPY ./bashrc /etc/skel/.bashrc

# svn
COPY ./wandisco.repo /etc/yum.repos.d/wandisco.repo
RUN rpm --import http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco \
	&& yum install --enablerepo=wandisco-svn -y subversion \
    && yum clean all

# initialize for ssh
RUN sed -i '/pam_loginuid\.so/s/required/optional/' /etc/pam.d/sshd \
	&& /sbin/service sshd start \
	&& /sbin/service sshd stop

# create login user
RUN useradd -d /home/docker -m -s /bin/bash docker \
	&& echo docker:tcuser | chpasswd \
	&& echo 'docker ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

EXPOSE 22

CMD ["/usr/sbin/sshd","-D"]
