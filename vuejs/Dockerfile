# This is a Dockerfile of "centos7_base"
FROM akkytech0617/my_centos7:nodejs12
MAINTAINER Akihide Ito akkytech0617@gmail.com
# install node.js 12.x
RUN curl -sL https://rpm.nodesource.com/setup_12.x | bash -
RUN yum -y install nodejs
# install vue.js
RUN npm install -g @vue/cli @vue/cli-init