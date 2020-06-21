# This is a Dockerfile of "centos7_base"
FROM akkytech0617/my_centos7:nodejs12
LABEL maintainer="akkytech0617@gmail.com"
# Create user for vscode remote development
ARG DOCKER_UID=1000
ARG DOCKER_USER=vscode
ARG DOCKER_PASSWORD=vscode
RUN useradd -m --uid ${DOCKER_UID} --groups sudo ${DOCKER_USER} \
  && echo ${DOCKER_USER}:${DOCKER_PASSWORD} | chpasswd
# install vue.js vue-cli
RUN npm install -g @vue/cli @vue/cli-init