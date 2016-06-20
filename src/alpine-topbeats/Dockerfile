FROM quay.io/stonevil/alpine-glibc
MAINTAINER stonevil@gmail.com

ENV TOPBEAT_VERSION=${TOPBEAT_VERSION:-1.2.3}
ENV TOPBEAT_DOWNLOAD_URL=https://download.elastic.co/beats/topbeat/topbeat-${TOPBEAT_VERSION}-x86_64.tar.gz
ENV TOPBEAT_BASE_PATH=/opt
ENV TOPBEAT_PATH ${TOPBEAT_BASE_PATH}/topbeat
ENV PATH ${TOPBEAT_PATH}:$PATH

LABEL com.stonevil.topbeat.version=${TOPBEAT_VERSION}

RUN mkdir -p ${TOPBEAT_BASE_PATH} && \
  ${SOFTWARE_DOWNLOAD_CMD} ${TOPBEAT_DOWNLOAD_URL} | tar -zxvf - -C ${TOPBEAT_BASE_PATH} && \
  mv ${TOPBEAT_BASE_PATH}/topbeat-${TOPBEAT_VERSION}-x86_64 ${TOPBEAT_PATH} && \
  adduser -D -H -h /data/topbeat -s /bin/bash topbeat && \
  chown -Rf topbeat ${TOPBEAT_PATH} && \
  ${APK_CACHE_RM}

ADD _rootfs /
ONBUILD ADD _rootfs_vendor /

VOLUME ["/data/topbeat"]

ENV ELASTIC_URL=${ELASTIC_URL:-localhost:9200}
ENV LOGSTASH_URL=${LOGSTASH_URL:-localhost:5044}

CMD chown -fR topbeat:topbeat /data/topbeat ${TOPBEAT_PATH} && \
  sed -i -e 's|_ELASTICURL_|'"${ELASTIC_URL}"'|g' -e 's|_LOGSTASHURL_|'"${LOGSTASH_URL}"'|g' ${TOPBEAT_PATH}/topbeat.yml && \
  gosu topbeat topbeat -e -c ${TOPBEAT_PATH}/topbeat.yml
