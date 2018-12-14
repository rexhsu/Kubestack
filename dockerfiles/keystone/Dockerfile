FROM rexhsu/ubuntu:xenial 
MAINTAINER rexhsu "rexhsu@qnap.com"

ENV KEYSTONE_BASEDIR=/opt/keystone

RUN set -x \
    && apt update -y \
    && apt install -y libffi-dev python-pip python-dev libssl-dev mysql-client python-mysqldb git \
    && apt clean -y

RUN git clone --branch $VERSION --depth 1 https://github.com/openstack/keystone.git ${KEYSTONE_BASEDIR} \ 
    && cd ${KEYSTONE_BASEDIR} \
    && pip install -r requirements.txt \
    && pip install . \
    && pip install uwsgi MySQL-python \
    && cp -r etc /etc/keystone \
    && pip install python-openstackclient \
    && apt remove -y python-dev \
    && apt autoremove -y 

COPY keystone.conf /etc/keystone/keystone.conf
COPY keystone.sql /root/keystone.sql

# Add bootstrap script and make it executable
COPY bootstrap.sh /etc/bootstrap.sh
RUN chown root:root /etc/bootstrap.sh && chmod a+x /etc/bootstrap.sh

ENTRYPOINT ["/etc/bootstrap.sh"]
EXPOSE 5000 35357

HEALTHCHECK --interval=10s --timeout=5s \
  CMD curl -f http://localhost:5000/v3 2> /dev/null || exit 1; \
  curl -f http://localhost:35357/v3 2> /dev/null || exit 1; \
