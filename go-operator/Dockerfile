FROM golang:1.22-bookworm

ENV ARCH=amd64

RUN apt-get update -qq && apt-get install -y --no-install-recommends \
    xz-utils \
    unzip \
    zstd \
    jq \
    patch \
    && rm -rf /var/lib/apt/lists/*

# install yq
RUN GO111MODULE=on go install github.com/mikefarah/yq/v4@v4.27.5

# set up helm
ENV HELM_VERSION=v3.5.4
ENV HELM_URL=https://get.helm.sh/helm-${HELM_VERSION}-linux-${ARCH}.tar.gz
RUN mkdir /usr/tmp && \
    curl ${HELM_URL} | tar xvzf - --strip-components=1 -C /usr/tmp/ && \
    mv /usr/tmp/helm /usr/bin/helm

## install docker client
RUN apt-get update -qq && apt-get install -y --no-install-recommends \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    rsync \
    && rm -rf /var/lib/apt/lists/*; \
    \
    curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add - >/dev/null; \
    echo "deb [arch=$(dpkg --print-architecture)] https://download.docker.com/linux/debian buster stable" > /etc/apt/sources.list.d/docker.list; \
    \
    apt-get update -qq && apt-get install -y --no-install-recommends \
    docker-ce=5:20.10.* \
    && rm -rf /var/lib/apt/lists/*

## install golangci
RUN curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sh -s v1.57.1

## install controller-gen
RUN GO111MODULE=on go install sigs.k8s.io/controller-tools/cmd/controller-gen@v0.15.0

## install ginkgo
RUN GO111MODULE=on go install github.com/onsi/ginkgo/v2/ginkgo@v2.1.4

# install openapi-gen
RUN  GO111MODULE=on go install k8s.io/code-generator/cmd/openapi-gen@v0.23.7

# install kind
RUN curl -Lo /usr/bin/kind https://kind.sigs.k8s.io/dl/v0.14.0/kind-linux-${ARCH} && chmod +x /usr/bin/kind
