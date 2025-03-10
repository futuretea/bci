FROM debian:bookworm

ENV ARCH=amd64

RUN apt-get update -qq && apt-get install -y --no-install-recommends \
    xz-utils \
    unzip \
    zstd \
    jq \
    patch \
    && rm -rf /var/lib/apt/lists/*

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