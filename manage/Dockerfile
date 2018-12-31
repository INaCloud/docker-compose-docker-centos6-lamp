FROM takaya030/php:5.5
MAINTAINER takaya030

# add external repos
RUN yum install -y http://dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm \
	&& sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/mysql-community.repo \
	&& yum install -y https://centos6.iuscommunity.org/ius-release.rpm \
	&& sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/ius.repo

# install packages
RUN yum install -y openssh-server openssh-clients graphviz graphviz-gd \
	&& yum install --enablerepo=mysql56-community -y mysql-community-client \
	&& yum install --enablerepo=ius -y git2u \
	&& yum clean all

# composer, phpunit
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
	&& curl -L https://phar.phpunit.de/phpunit-old.phar > /usr/local/bin/phpunit \
	&& chmod +x /usr/local/bin/phpunit

# for git prompt
RUN curl -L https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash > /etc/skel/.git-completion.bash && \
	curl -L https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh > /etc/skel/.git-prompt.sh 
ADD ./bashrc /etc/skel/.bashrc

# svn
ADD ./wandisco.repo /etc/yum.repos.d/wandisco.repo
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
