FROM node:6-slim

RUN apt update \
  && apt install --fix-missing -y \
    vim.tiny \
    git \
  && apt clean

RUN npm install lerna -g

RUN git config --global user.email "you@example.com" \
&&  git config --global user.name "Your Name" \
&&  git config --global core.autocrlf true

env DEBUG=rhea*,azure*
EXPOSE 9229
EXPOSE 8080

# phase 1: grab the base branch and run prebuild
ARG HORTON_REPO
WORKDIR /sdk
RUN git clone https://github.com/$HORTON_REPO .
RUN git submodule update --init --recursive
COPY ./prebuild.sh /
RUN /prebuild.sh

# phase 2: grab the code we want to test and rebuild
ARG HORTON_COMMIT_NAME
ARG HORTON_COMMIT_SHA
ARG HORTON_FORCEFLAG
VOLUME /t
COPY ./source.tar.gz /t/
COPY ./fetch.sh /
RUN /fetch.sh /t
COPY ./rebuild.sh /
RUN /rebuild.sh

ENTRYPOINT ["/usr/local/bin/node", "/sdk/edge-e2e/wrapper/nodejs-server-server/index.js"]
