# This is a Dockerfile of "centos7_base"
FROM centos:centos7
LABEL maintainer="akkytech0617@gmail.com"
# update os
RUN yum -y update 
# install deltarpm, yum
RUN yum -y install deltarpm \
    sudo
# install git2.24
RUN yum -y install https://repo.ius.io/ius-release-el7.rpm \
 && yum -y install git224
# setup config of git
RUN git config --global user.name "Akihide Ito" \
 && git config --global user.email "akkytech0617@gmail.com" \
 && git config --global push.default current \
 && git config --global core.ignorecase false \
 && git config --global core.quotepath false \
 && git config --global color.ui true