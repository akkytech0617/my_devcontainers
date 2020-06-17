# This is a Dockerfile of "centos7_base"
FROM centos7:base
MAINTAINER Akihide Ito akkytech0617@gmail.com
# install node.js 12.x
RUN curl -sL https://rpm.nodesource.com/setup_12.x | bash -
RUN yum -y install nodejs
# install vue.js
RUN npm install -g @vue/cli @vue/cli-init
