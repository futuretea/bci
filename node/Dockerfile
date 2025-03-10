FROM futuretea/bookworm:master-head

ENV NVM_DIR /usr/local/nvm
RUN mkdir -p $NVM_DIR && \
    curl -sL https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash && \
    . "$NVM_DIR/nvm.sh" && \
    nvm install v20.18.3 && \
    npm install -g npm@11.2.0 && \
    npm install -g yarn@1.22.22