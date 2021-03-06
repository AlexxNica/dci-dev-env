FROM centos:7

LABEL name="DCI SWIFT" version="0.0.1"
MAINTAINER DCI Team <distributed-ci@redhat.com>

ENV container docker

RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;

VOLUME [ "/sys/fs/cgroup" ]

RUN yum install -y centos-release-openstack-ocata && \
    yum install -y openstack-swift openstack-swift-proxy memcached \
    openstack-swift-object openstack-swift-container openstack-swift-account \
    openstack-keystone mariadb-server httpd mod_wsgi python-swiftclient \
    python-openstackclient openssh-server && \
    yum clean all

RUN echo root:root | chpasswd && ssh-keygen -b 2048 -t rsa -f ~/.ssh/id_rsa -N ''

ADD conf/bootstrap.sh /sbin/bootstrap.sh
ADD conf/systemd-bootstrap /etc/systemd/system/systemd-bootstrap.service
ADD conf/wsgi-keystone.conf /etc/httpd/conf.d/
ADD conf/account-server.conf /etc/swift/
ADD conf/object-server.conf /etc/swift/
ADD conf/container-server.conf /etc/swift/
ADD conf/proxy-server.conf /etc/swift/
ADD conf/keystone.conf /etc/keystone/

RUN chmod +x /sbin/bootstrap.sh

RUN systemctl enable sshd systemd-bootstrap

EXPOSE 5000 8080 35357

CMD ["/sbin/bootstrap.sh"]
