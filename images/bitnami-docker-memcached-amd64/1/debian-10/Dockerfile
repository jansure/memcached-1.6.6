FROM harbor-b.alauda.cn/devops/builder-gcc:amd64 as builder-gcc-amd64

ENV HOME="/" \
    OS_ARCH="amd64" \
    OS_FLAVOUR="debian-10" \
    OS_NAME="linux"

ENV LD_LIBRARY_PATH=$HOME/memcached:/opt/bitnami/memcached/lib/:$LD_LIBRARY_PATH
ENV LD_LIBRARY_PATH=.:$LD_LIBRARY_PATH
RUN export LD_LIBRARY_PATH
RUN export HOME

RUN cd $HOME
RUN echo $HOME
RUN mkdir -p $HOME/memcached
COPY memcached $HOME/memcached
RUN cd $HOME/memcached/
RUN cd $HOME/memcached/libevent-2.1.11-stable && pwd && autogen.sh && ls -lrt configure && configure && cat Makefile &&make clean && make && make install

RUN ls -lrt $HOME/memcached/libevent-2.1.11-stable/.libs
RUN ls -lrt $HOME/memcached/libevent-2.1.11-stable/.libs/libevent-2.1.so.7
RUN cp $HOME/memcached/libevent-2.1.11-stable/.libs/libevent-2.1.so.7 $HOME/memcached/
RUN cd $HOME/memcached/ && ./autogen.sh && ./configure --with-libevent=$HOME/memcached/libevent-2.1.11-stable/.libs/ && make clean && make
RUN ldd -r $HOME/memcached/memcached
RUN ldd -r $HOME/memcached/memcached-debug
RUN mkdir -p /workspace/bin && mkdir -p /workspace/lib
RUN cp $HOME/memcached/libevent-2.1.11-stable/.libs/libevent-2.1.so.7 /workspace/lib
RUN cp $HOME/memcached/memcached /workspace/bin && cp $HOME/memcached/memcached-debug /workspace/bin
RUN cp -rf $HOME/memcached/images/bitnami-docker-memcached-amd64/1/debian-10/prebuildfs /workspace
RUN cp -rf $HOME/memcached/images/bitnami-docker-memcached-amd64/1/debian-10/rootfs /workspace
RUN cp -rf $HOME/memcached/images/bitnami-docker-memcached-amd64/1/debian-10/docker-compose.yml /workspace


FROM docker.io/bitnami/minideb:buster
LABEL maintainer "Bitnami <containers@bitnami.com>"

ENV HOME="/" \
    OS_ARCH="amd64" \
    OS_FLAVOUR="debian-10" \
    OS_NAME="linux"

RUN mkdir -p /workspace
RUN mkdir -p /workspace/prebuildfs
COPY --from=builder-gcc-amd64 /workspace/prebuildfs /
COPY --from=builder-gcc-amd64 /workspace/rootfs /
COPY --from=builder-gcc-amd64 /workspace /workspace

# Install required system packages and dependencies
RUN install_packages acl ca-certificates curl gzip libc6 libevent-2.1-6 libsasl2-2 libsasl2-modules procps sasl2-bin tar
RUN cd /workspace
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "memcached" "1.6.8-0" --checksum 922c3d0c8f3dd9e9bb0a2654ab46f4788fc31fa65c0b4049a09fb93118ca3a66
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "gosu" "1.12.0-2" --checksum 4d858ac600c38af8de454c27b7f65c0074ec3069880cb16d259a6e40a46bbc50
RUN apt-get update && apt-get upgrade -y && \
    rm -r /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami
RUN ln -s /opt/bitnami/scripts/memcached/entrypoint.sh /entrypoint.sh
RUN ln -s /opt/bitnami/scripts/memcached/run.sh /run.sh

COPY --from=builder-gcc-amd64 /workspace/rootfs /
RUN /opt/bitnami/scripts/memcached/postunpack.sh
ENV BITNAMI_APP_NAME="memcached" \
    BITNAMI_IMAGE_VERSION="1.6.8-debian-10-r7" \
    PATH="/opt/bitnami/memcached/bin:/opt/bitnami/common/bin:$PATH"

RUN mkdir -p /opt/bitnami/memcached/lib/
RUN mkdir -p /opt/bitnami/memcached/bin
RUN cp -rf /workspace/lib/* /opt/bitnami/memcached/lib/
RUN cp -rf /workspace/bin/* /opt/bitnami/memcached/bin/
ENV LD_LIBRARY_PATH=/opt/bitnami/memcached/lib/:$LD_LIBRARY_PATH
RUN export LD_LIBRARY_PATH

EXPOSE 11211

USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/memcached/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/memcached/run.sh" ]
