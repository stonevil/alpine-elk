FROM quay.io/stonevil/alpine-jre
MAINTAINER stonevil@gmail.com

# Set environment variables
ENV ELASTIC_VERSION=${ELASTIC_VERSION:-2.3.3}
ENV ELASTIC_DOWNLOAD_URL=${ELASTIC_DOWNLOAD_URL:-https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-$ELASTIC_VERSION.tar.gz}
ENV ELASTIC_BASE_PATH=/opt
ENV ELASTIC_PATH $ELASTIC_BASE_PATH/elastic
ENV PATH $ELASTIC_PATH/bin:$PATH

ENV ELASTIC_NAME=${ELASTIC_NAME:-elastic}

LABEL com.stonevil.elastic.version=${ELASTIC_VERSION}

RUN ${SOFTWARE_DOWNLOAD_CMD} ${ELASTIC_DOWNLOAD_URL} | tar -zxvf - -C ${ELASTIC_BASE_PATH} && \
  mv ${ELASTIC_BASE_PATH}/elasticsearch-${ELASTIC_VERSION} ${ELASTIC_PATH} && \
  rm -rf $(find ${ELASTIC_PATH} | egrep "(\.(exe|bat)$|sigar/.*(dll|winnt|x86-linux|solaris|ia64|freebsd|macosx))") && \
  adduser -D -H -h /data/elastic -s /bin/bash elastic && \
  chown -Rf elastic ${ELASTIC_PATH} && \
  ${APK_CACHE_RM} && \
  cd ${ELASTIC_PATH} && \
  ./bin/plugin install royrusso/elasticsearch-HQ

ADD _rootfs /
ONBUILD ADD _rootfs_vendor /

EXPOSE 9200 9300

VOLUME ["/data/elastic"]

ENV ELASTIC_NAME=${ELASTIC_NAME:-elastic}
ENV ELASTIC_NODE_MASTER=${ELASTIC_NODE_MASTER:-true}
ENV ELASTIC_NODE_DATA=${ELASTIC_NODE_DATA:-true}
ENV ELASTIC_HTTP_ENABLE=${ELASTIC_HTTP_ENABLE:-true}
ENV ELASTIC_MULTICAST=${ELASTIC_MULTICAST:-true}

CMD mkdir -p /data/elastic/data /data/elastic/logs /data/elastic/work ${ELASTIC_PATH}/config/scripts; chown -fR elastic:elastic /data/elastic ${ELASTIC_PATH} && \
  (ulimit -n 65536; if [ `ulimit -n` -lt "65536" ] ; then (echo "Elastic recommends 64k open files per process. you have "`ulimit -n` && echo "the docker deamon should be run with increased file descriptors to increase those available in the container" && echo " try \`ulimit -n 65536\`"); fi) && \
  gosu elastic elasticsearch
