FROM quay.io/stonevil/alpine-elastic
MAINTAINER stonevil@gmail.com

# k8s
ENV container docker

ENV ELASTIC_K8S_PLUGIN_VERSION=${ELASTIC_K8S_PLUGIN_VERSION:-2.3.3}
ENV ELASTIC_K8S_PLUGIN_URL=${ELASTIC_K8S_PLUGIN_URL:-io.fabric8/elasticsearch-cloud-kubernetes/$ELASTIC_K8S_PLUGIN_VERSION}

# Temporary override elasticsearch.yml config to install Elastic k8s plugin
ADD _rootfs_temp /

LABEL com.stonevil.elastic_k8s_plugin.version=${ELASTIC_K8S_PLUGIN_VERSION}
LABEL com.stonevil.k8s.ready=true

RUN cd $ELASTIC_PATH && \
  ./bin/plugin install ${ELASTIC_K8S_PLUGIN_URL}

ADD _rootfs /
ONBUILD ADD _rootfs_vendor /

ENV ELASTIC_NAMESPACE=${ELASTIC_NAMESPACE:-default}
ENV ELASTIC_DISCOVERY_SERVICE=${ELASTIC_DISCOVERY_SERVICE:-elastic-discovery}
