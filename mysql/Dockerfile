FROM mysql:5.6
MAINTAINER takaya030

# timezone
RUN ln -sf /usr/share/zoneinfo/Japan /etc/localtime

# modify my.cnf
RUN sed -i -e "s/^sql_mode=.*$/sql_mode=NO_ENGINE_SUBSTITUTION/" /etc/mysql/my.cnf

