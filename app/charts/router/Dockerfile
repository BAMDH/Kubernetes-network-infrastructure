FROM centos:8

# Actualizar repos y paquetes porque sino da error
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-* \
    && sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-* \
    && yum -y update \
    && yum install -y nftables iproute iptables \
    && yum clean all \
    && rm -rf /var/cache/yum

CMD ["/bin/bash", "-c", "nft list ruleset; tail -f /dev/null"]
