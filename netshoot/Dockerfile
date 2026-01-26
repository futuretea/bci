FROM --platform=linux/amd64 ubuntu:22.04
RUN echo "wireshark-common wireshark-common/install-setuid boolean true" | debconf-set-selections
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        util-linux file unzip \
        gpg ca-certificates openssl \
        git zsh fzf vim jq \
        curl wget httpie \
        python3-pip python3-setuptools \
        scapy tcpdump tshark ngrep \
        strace bpfcc-tools \
        iftop iotop htop \
        bird bridge-utils ethtool \
        fping hping3 \
        iperf iperf3 netperf \
        net-tools iproute2 iputils-ping\
        netcat-openbsd socat telnet \
        tcptraceroute traceroute \
        ipset iptraf-ng \
        iptables nftables ipvsadm \
        mtr nmap lsof \
        swaks nfs-common \
        dhcping dnsutils \
        jattach \
    && apt-get clean
ENV GOREPLAY_VERSION=1.3.3
ENV PWRU_VERSION=v1.0.9
RUN curl -sLO https://github.com/buger/goreplay/releases/download/${GOREPLAY_VERSION}/gor_${GOREPLAY_VERSION}_amd64.deb \
    && dpkg -i gor_${GOREPLAY_VERSION}_amd64.deb \
    && rm gor_${GOREPLAY_VERSION}_amd64.deb \
    && curl -sLO https://github.com/cilium/pwru/releases/download/${PWRU_VERSION}/pwru-linux-amd64.tar.gz \
    && tar -xvf pwru-linux-amd64.tar.gz \
    && mv pwru /usr/local/bin/pwru \
    && rm -rf pwru-linux-amd64.tar.gz