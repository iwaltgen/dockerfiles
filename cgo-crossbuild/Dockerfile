# ref https://github.com/docker-library/golang/blob/master/1.13/buster/Dockerfile
FROM buildpack-deps:buster-scm

# gcc for cgo
RUN apt-get update && apt-get install -y --no-install-recommends \
		g++ \
		gcc \
		libc6-dev \
		make \
		pkg-config \
		llvm \
		mingw-w64 \
	&& rm -rf /var/lib/apt/lists/*

# ref https://github.com/steeve/cross-compiler/blob/master/darwin-x64/Dockerfile
ENV CROSS_TRIPLE x86_64-apple-darwin14
ENV CROSS_ROOT /usr/${CROSS_TRIPLE}
ENV PATH ${PATH}:${CROSS_ROOT}/bin
# ENV LD_LIBRARY_PATH /usr/lib/llvm-3.4/lib:${CROSS_ROOT}/lib
ENV MAC_SDK_VERSION 10.10

RUN curl -L https://github.com/tpoechtrager/osxcross/archive/master.tar.gz | tar xvz && \
    cd /osxcross-master/ && \
    curl -L -o tarballs/MacOSX${MAC_SDK_VERSION}.sdk.tar.xz https://www.dropbox.com/s/yfbesd249w10lpc/MacOSX${MAC_SDK_VERSION}.sdk.tar.xz && \
    echo | SDK_VERSION=${MAC_SDK_VERSION} OSX_VERSION_MIN=10.6 ./build.sh && \
    mv /osxcross-master/target ${CROSS_ROOT} && \
    mkdir -p ${CROSS_ROOT}/lib && \
    rm -rf /osxcross-master

# ref https://github.com/multiarch/crossbuild/blob/master/Dockerfile
# Install OSx cross-tools

# Build arguments
# ARG osxcross_repo="tpoechtrager/osxcross"
# ARG osxcross_revision="a845375e028d29b447439b0c65dea4a9b4d2b2f6"
# ARG darwin_sdk_version="10.10"
# ARG darwin_osx_version_min="10.6"
# ARG darwin_version="14"
# ARG darwin_sdk_url="https://www.dropbox.com/s/yfbesd249w10lpc/MacOSX${darwin_sdk_version}.sdk.tar.xz"

# # ENV available in docker image
# ENV OSXCROSS_REPO="${osxcross_repo}" \
#     OSXCROSS_REVISION="${osxcross_revision}" \
#     DARWIN_SDK_VERSION="${darwin_sdk_version}" \
#     DARWIN_VERSION="${darwin_version}" \
#     DARWIN_OSX_VERSION_MIN="${darwin_osx_version_min}" \
#     DARWIN_SDK_URL="${darwin_sdk_url}"

# RUN mkdir -p "/tmp/osxcross" \
# 	&& cd "/tmp/osxcross" \
# 	&& curl -sLo osxcross.tar.gz "https://codeload.github.com/${OSXCROSS_REPO}/tar.gz/${OSXCROSS_REVISION}" \
# 	&& tar --strip=1 -xzf osxcross.tar.gz \
# 	&& rm -f osxcross.tar.gz \
# 	&& curl -sLo tarballs/MacOSX${DARWIN_SDK_VERSION}.sdk.tar.xz "${DARWIN_SDK_URL}" \
# 	&& yes "" | SDK_VERSION="${DARWIN_SDK_VERSION}" OSX_VERSION_MIN="${DARWIN_OSX_VERSION_MIN}" ./build.sh \
# 	&& mv target /usr/osxcross \
# 	&& mv tools /usr/osxcross/ \
# 	&& ln -sf ../tools/osxcross-macports /usr/osxcross/bin/omp \
# 	&& ln -sf ../tools/osxcross-macports /usr/osxcross/bin/osxcross-macports \
# 	&& ln -sf ../tools/osxcross-macports /usr/osxcross/bin/osxcross-mp \
# 	&& rm -rf /tmp/osxcross \
# 	&& rm -rf "/usr/osxcross/SDK/MacOSX${DARWIN_SDK_VERSION}.sdk/usr/share/man"

# # Create symlinks for triples and set default CROSS_TRIPLE
# ENV LINUX_TRIPLES=aarch64-linux-gnu	\
#     DARWIN_TRIPLES=x86_64-apple-darwin${DARWIN_VERSION}	\
#     WINDOWS_TRIPLES=x86_64-w64-mingw32 \
#     CROSS_TRIPLE=x86_64-linux-gnu

# COPY osxcross-wrapper.sh /usr/bin/osxcross-wrapper
# RUN for triple in $(echo ${LINUX_TRIPLES} | tr "," " "); do                                       \
#       for bin in /etc/alternatives/$triple-* /usr/bin/$triple-*; do                               \
#         if [ ! -f /usr/$triple/bin/$(basename $bin | sed "s/$triple-//") ]; then                  \
#           ln -s $bin /usr/$triple/bin/$(basename $bin | sed "s/$triple-//");                      \
#         fi;                                                                                       \
#       done;                                                                                       \
#     done &&                                                                                       \
#     for triple in $(echo ${DARWIN_TRIPLES} | tr "," " "); do                                      \
#       mkdir -p /usr/$triple/bin;                                                                  \
#       for bin in /usr/osxcross/bin/$triple-*; do                                                  \
#         ln /usr/bin/osxcross-wrapper /usr/$triple/bin/$(basename $bin | sed "s/$triple-//");      \
#       done &&                                                                                     \
#       rm -f /usr/$triple/bin/clang*;                                                              \
#       ln -s cc /usr/$triple/bin/gcc;                                                              \
#       ln -s /usr/osxcross/SDK/MacOSX${DARWIN_SDK_VERSION}.sdk/usr /usr/x86_64-linux-gnu/$triple;  \
#     done;                                                                                         \
#     for triple in $(echo ${WINDOWS_TRIPLES} | tr "," " "); do                                     \
#       mkdir -p /usr/$triple/bin;                                                                  \
#       for bin in /etc/alternatives/$triple-* /usr/bin/$triple-*; do                               \
#         if [ ! -f /usr/$triple/bin/$(basename $bin | sed "s/$triple-//") ]; then                  \
#           ln -s $bin /usr/$triple/bin/$(basename $bin | sed "s/$triple-//");                      \
#         fi;                                                                                       \
#       done;                                                                                       \
#       ln -s gcc /usr/$triple/bin/cc;                                                              \
#       ln -s /usr/$triple /usr/x86_64-linux-gnu/$triple;                                           \
#     done
# we need to use default clang binary to avoid a bug in osxcross that recursively call himself
# with more and more parameters

ENV GOLANG_VERSION 1.13.4

RUN set -eux; \
	# this "case" statement is generated via "update.sh"
	dpkgArch="$(dpkg --print-architecture)"; \
	case "${dpkgArch##*-}" in \
		amd64) goRelArch='linux-amd64'; goRelSha256='692d17071736f74be04a72a06dab9cac1cd759377bd85316e52b2227604c004c' ;; \
		armhf) goRelArch='linux-armv6l'; goRelSha256='9f76e6353c9ae2dcad1731b7239531eb8be2fe171f29f2a9c5040945a930fd41' ;; \
		arm64) goRelArch='linux-arm64'; goRelSha256='8b8d99eb07206f082468fb4d0ec962a819ae45d54065fc1ed6e2c502e774aaf0' ;; \
		i386) goRelArch='linux-386'; goRelSha256='497934398ca57c7c207ce3388f099823923b4c7b74394d6ed64cd2d3751aecb8' ;; \
		ppc64el) goRelArch='linux-ppc64le'; goRelSha256='815bf3c7100e73cfac50c4a07c8eeb4b0458a49ffa0e13a74a6cf7ad8e2a6499' ;; \
		s390x) goRelArch='linux-s390x'; goRelSha256='efc6947e8eb0a6409f4c8ba62b00ae4e54404064bc221df1b73364a95945a350' ;; \
		*) goRelArch='src'; goRelSha256='95dbeab442ee2746b9acf0934c8e2fc26414a0565c008631b04addb8c02e7624'; \
			echo >&2; echo >&2 "warning: current architecture ($dpkgArch) does not have a corresponding Go binary release; will be building from source"; echo >&2 ;; \
	esac; \
	\
	url="https://golang.org/dl/go${GOLANG_VERSION}.${goRelArch}.tar.gz"; \
	wget -O go.tgz "$url"; \
	echo "${goRelSha256} *go.tgz" | sha256sum -c -; \
	tar -C /usr/local -xzf go.tgz; \
	rm go.tgz; \
	\
	if [ "$goRelArch" = 'src' ]; then \
		echo >&2; \
		echo >&2 'error: UNIMPLEMENTED'; \
		echo >&2 'TODO install golang-any from jessie-backports for GOROOT_BOOTSTRAP (and uninstall after build)'; \
		echo >&2; \
		exit 1; \
	fi; \
	\
	export PATH="/usr/local/go/bin:$PATH"; \
	go version

ENV GOPATH /go
ENV PATH $GOPATH/bin:/usr/local/go/bin:$PATH

RUN mkdir -p "$GOPATH/src" "$GOPATH/bin" && chmod -R 777 "$GOPATH"
WORKDIR $GOPATH

RUN curl -sfL https://install.goreleaser.com/github.com/goreleaser/goreleaser.sh | sh

WORKDIR /workspace
