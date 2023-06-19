# Dockerfile for VMware Tanzu Kubernetes Grid Bootstrap
#
# Includes the libraries necessary to run TKG and connect to the 
# management UI for deploying a TKG Management Cluster.

FROM ubuntu:latest
MAINTAINER Chris Mutchler <chris@virtualelephant.com>

RUN apt-get -y update
RUN \
  apt-get -y dist-upgrade && \
  apt-get -y install software-properties-common curl ca-certificates gnupg apt-transport-https

# Install Carvel tools from carvel.io
RUN curl -s -L github.com/carvel-dev/ytt/releases/download/v0.45.0/ytt-linux-amd64 > /tmp/ytt
RUN mv /tmp/ytt /usr/local/bin/ytt
RUN chmod +x /usr/local/bin/ytt

RUN curl -s -L github.com/carvel-dev/ytt/releases/download/v0.45.0/imgpkg-linux-amd64 > /tmp/imgpkg
RUN mv /tmp/imgpkg /usr/local/bin/imgpkg
RUN chmod +x /usr/local/bin/imgpkg

RUN curl -s -L github.com/carvel-dev/ytt/releases/download/v0.45.0/kbld-linux-amd64 > /tmp/kbld
RUN mv /tmp/kbld /usr/local/bin/kbld
RUN chmod +x /usr/local/bin/kbld

RUN curl -s -L github.com/carvel-dev/ytt/releases/download/v0.45.0/kapp-linux-amd64 > /tmp/kapp
RUN mv /tmp/kapp /usr/local/bin/kapp
RUN chmod +x /usr/local/bin/kapp

# Install kubectl from VMware
RUN curl -s -L https://packages.cloud.google.com/apt/doc/apt-key.gpg | gpg --dearmour -o /etc/apt/trusted.gpg.d/kubernetes-xenial.gpg
RUN apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
RUN apt-get install -y kubectl
