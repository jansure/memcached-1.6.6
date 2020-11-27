#FROM docker.io/adaptant/minideb:arm64
#FROM debian@sha256:f1fc8f06010702142bff72164336b7eb91bde916b6f6e2697ac41adbe03939f5
FROM docker.io/debian:stable

LABEL maintainer "Bitnami <containers@bitnami.com>"

ENV HOME="/" \
    OS_ARCH="arm64" \
    OS_FLAVOUR="arm64" \
    OS_NAME="linux"

COPY prebuildfs /
# Install required system packages and dependencies
RUN apt-get update -y
RUN apt-get install -y acl ca-certificates curl gzip libc6 libevent-2.1-6 libsasl2-2 libsasl2-modules procps sasl2-bin tar
#RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "memcached" "1.6.8-0" --checksum 922c3d0c8f3dd9e9bb0a2654ab46f4788fc31fa65c0b4049a09fb93118ca3a66
#RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "gosu" "1.12.0-2" --checksum 4d858ac600c38af8de454c27b7f65c0074ec3069880cb16d259a6e40a46bbc50
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "memcached" "1.6.8-0" 
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "gosu" "1.12.0-2" 
RUN apt-get update && apt-get upgrade -y && \
    rm -r /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami
RUN ln -s /opt/bitnami/scripts/memcached/entrypoint.sh /entrypoint.sh
RUN ln -s /opt/bitnami/scripts/memcached/run.sh /run.sh

COPY rootfs /
RUN /opt/bitnami/scripts/memcached/postunpack.sh
ENV BITNAMI_APP_NAME="memcached" \
    BITNAMI_IMAGE_VERSION="arm64" \
    PATH="/opt/bitnami/memcached/bin:/opt/bitnami/common/bin:$PATH"
COPY bin /opt/bitnami/memcached/bin/
ENV LD_LIBRARY_PATH=/opt/bitnami/memcached/lib/:$LD_LIBRARY_PATH

COPY lib /opt/bitnami/memcached/lib/

EXPOSE 11211

USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/memcached/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/memcached/run.sh" ]
