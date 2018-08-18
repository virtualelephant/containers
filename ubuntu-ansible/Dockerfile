# Dockerfile for creating an Ansible Control Server with
# the VMware modules necessary to build a complete SDDC
# Blog details available: http://virtualelphant.com

FROM ubuntu:artful
MAINTAINER Chris Mutchler <chris@virtualelephant.com>

RUN apt-get -y update
RUN \
  apt-get -y dist-upgrade && \
  apt-get -y install software-properties-common python-software-properties vim bind9utils bind9-host && \
  apt-add-repository ppa:ansible/ansible

# Install packages needed for NSX modules in Ansible
RUN \
  apt-get -y update && \
  apt-get -y install ansible python-pip python-dev libxml2 libxml2-dev libxslt1-dev zlib1g-dev npm git && \
  pip install --upgrade pyvmomi && \
  pip install pysphere && \
  pip install nsxramlclient && \
  pip install yamllint && \
  npm install -g https://github.com/yfauser/raml2html && \
  npm  install -g raml-fleece

# Copy customized ansible.cfg file to /etc/ansible
RUN mkdir -p /opt/ansible/modules /opt/ansible/modules/nsxansible /opt/ansible/modules/openshift/ /opt/ansible/modules/ansible-modules-extras
RUN mkdir -p /opt/ansible/roles
RUN mkdir -p /opt/ansible/log
COPY ansible.cfg /etc/ansible/

# Add additional Ansible modules for NSX and VM folders
RUN \
  mkdir -p /opt/tmp && \
  git clone https://github.com/vmware/nsxansible /opt/tmp/nsxansible && \
  git clone https://github.com/vmware/ansible-modules-extras-gpl3.git /opt/tmp/ansible-modules-extras && \
  git clone https://github.com/openshift/openshift-ansible-contrib /opt/tmp/openshift-ansible-contrib && \
  git clone https://github.com/virtualelephant/nsxansible /opt/tmp/ve-nsxansible && \
  rm -rf /opt/tmp/nsxansible/library/__init__.py && \
  cp /opt/tmp/nsxansible/library/*.py /opt/ansible/modules/nsxansible/ && \
  cp /opt/tmp/ve-nsxansible/library/nsx_edge_firewall.py /opt/ansible/modules/nsxansible/ && \
  cp /opt/tmp/openshift-ansible-contrib/reference-architecture/vmware-ansible/playbooks/library/vmware*.py /opt/ansible/modules/openshift/

# Get NSXRAML
RUN \
  mkdir -p /opt/nsxraml/6.3 && \
  git clone https://github.com/vmware/nsxraml.git -b 6.3 /opt/nsxraml/6.3 && \
  mkdir -p /opt/nsxraml/6.4 && \
  git clone -b 6.4 https://github.com/vmware/nsxraml.git /opt/nsxraml/6.4 && \
  ln -s /opt/nsxraml/6.4 /opt/nsxraml/current

# Add vSAN Python API modules - must be done after pyVmomi installation
COPY vsanmgmtObjects.py /usr/lib/python2.7/
COPY vsanapiutils.py /usr/lib/python2.7/

# Add ovftool
COPY VMware-ovftool-4.3.0-8873523-lin.x86_64.bundle /var/tmp
RUN sh +x /var/tmp/VMware-ovftool-4.3.0-8873523-lin.x86_64.bundle --eulas-agreed

# Setup container to properly use SSH bastion host for Ansible
RUN mkdir /root/.ssh
RUN chmod 740 /root/.ssh
COPY config /root/.ssh/config
COPY ansible.cfg /etc/ansible/

# Container build cleanup
RUN rm -rf /opt/tmp/*

# Edit MOTD to give container consumer info
COPY motd /etc/motd
RUN echo '[ ! -z "$TERM" -a -r /etc/motd ] && cat /etc/issue && cat /etc/motd' >> /etc/bash.bashrc

# Default command if no command specified on the docker run command
CMD ["ansible", "--version"]
