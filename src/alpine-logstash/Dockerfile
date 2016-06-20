FROM quay.io/stonevil/alpine-jre
MAINTAINER stonevil@gmail.com

ENV LOGSTASH_VERSION=${LOGSTASH_VERSION:-2.3.3}
ENV LOGSTASH_DOWNLOAD_URL=${LOGSTASH_DOWNLOAD_URL:-https://download.elastic.co/logstash/logstash/logstash-$LOGSTASH_VERSION.tar.gz}
ENV LOGSTASH_BASE_PATH=/opt
ENV LOGSTASH_PATH ${LOGSTASH_BASE_PATH}/logstash
ENV PATH ${LOGSTASH_PATH}/bin:$PATH

ENV LOGSTASH_PLUGINS=${LOGSTASH_PLUGINS:-logstash-input-beats}

LABEL com.stonevil.logstash.version=${LOGSTASH_VERSION}

RUN mkdir -p ${LOGSTASH_BASE_PATH} && \
  ${SOFTWARE_DOWNLOAD_CMD} ${LOGSTASH_DOWNLOAD_URL} | tar -zxvf - -C ${LOGSTASH_BASE_PATH} && \
  mv ${LOGSTASH_BASE_PATH}/logstash-${LOGSTASH_VERSION} ${LOGSTASH_PATH} && \
  ${LOGSTASH_PATH}/bin/logstash-plugin install ${LOGSTASH_PLUGINS} && \
  rm -rf $(find ${LOGSTASH_PATH} | egrep "(\.(exe|bat)$|sigar/.*(dll|winnt|x86-linux|solaris|ia64|freebsd|macosx))") && \
  adduser -D -H -h /data/logstash -s /bin/bash logstash && \
  chown -Rf logstash ${LOGSTASH_PATH} && \
  ${APK_CACHE_RM}

ADD _rootfs /
ONBUILD ADD _rootfs_vendor /

EXPOSE 5044 5044/udp 8080

VOLUME ["/data/logstash"]

ENV ELASTIC_URL=${ELASTIC_URL:-localhost:9200}

CMD mkdir -p /data/logstash; chown -fR logstash:logstash /data/logstash ${LOGSTASH_PATH} && \
  sed -i -e 's|_ELASTICURL_|'"${ELASTIC_URL}"'|g' ${LOGSTASH_PATH}/etc/logstash.conf && \
  sed -i -e 's|_ELASTICURL_|'"${ELASTIC_URL}"'|g' ${LOGSTASH_PATH}/etc/journal.conf && \
  cd ${LOGSTASH_PATH} && gosu logstash ./bin/logstash -f ${LOGSTASH_PATH}/etc/logstash.conf
