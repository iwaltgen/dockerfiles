# Dockerfile for docker:stable-dind
FROM docker:stable-dind

RUN apk update && \
    apk upgrade --no-cache && \
    apk add --no-cache py-pip && \
    pip install --no-cache-dir docker-compose
