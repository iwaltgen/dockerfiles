FROM minio/mc

LABEL org.opencontainers.image.source https://github.com/iwaltgen/dockerfiles

RUN \
    microdnf update --nodocs && \
    microdnf install tar && \
    microdnf clean all
