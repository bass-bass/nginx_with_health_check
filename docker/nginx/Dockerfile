FROM centos:7

WORKDIR /work

COPY nginx_upstream_check_module /work/nginx_upstream_check_module

RUN yum -y update && yum clean all && \
    yum -y install sudo vim wget make patch gcc pcre-devel zlib-devel && \
    wget "http://nginx.org/download/nginx-1.21.3.tar.gz" && \
    tar -xvzf nginx-1.21.3.tar.gz && \
    cd nginx-1.21.3 && \
    patch -p1 < ../nginx_upstream_check_module/check_1.20.1+.patch && \
    ./configure --add-module=../nginx_upstream_check_module && \
    make && \
    make install && \
    ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx && \
    useradd -s /bin/false nginx && \
    mkdir -p /var/log/nginx


COPY nginx.conf /etc/nginx/
COPY startup.sh /root/

CMD ["/bin/bash", "/root/startup.sh"]
