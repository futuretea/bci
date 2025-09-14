FROM --platform=linux/amd64 registry.suse.com/bci/bci-base:15.7

RUN zypper -n rm container-suseconnect && \
    zypper -n install curl gzip tar nfs-client && \
    zypper -n clean -a && rm -rf /tmp/* /var/tmp/* /usr/share/doc/packages/* && \
    curl -sLf https://github.com/krallin/tini/releases/download/v0.19.0/tini > /usr/bin/tini && chmod +x /usr/bin/tini