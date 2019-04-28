
# Dockerfile for jenkins-dind [jenkins:lts - v2.164.2]
FROM jenkins/jenkins:lts

ARG GOSU_VERSION=1.10
ARG DOCKER_COMPOSE_VERSION=1.21.2

# switch to root, let the entrypoint drop back to jenkins
USER root

# install debian packages, gosu, and docker, docker-compose
RUN apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
     vim \
     wget \
     jq \
 && dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')" \
 && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch" \
 && chmod +x /usr/local/bin/gosu \
 && wget -O /usr/local/bin/mc https://dl.minio.io/client/mc/release/linux-amd64/mc \
 && chmod +x /usr/local/bin/mc \
 && gosu nobody true \
 && curl -sSL https://get.docker.com/ | sh \
 && curl -sSL https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose \
 && chmod +x /usr/local/bin/docker-compose \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# entrypoint is used to update docker gid and revert back to jenkins user
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
