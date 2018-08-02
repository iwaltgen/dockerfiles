# Dockerfile for filebrowser

FROM ubuntu:latest as builder
RUN useradd -u 1003 jenkins

FROM hacdias/filebrowser
COPY --from=builder /etc/passwd /etc/passwd
USER jenkins
