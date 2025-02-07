# Use CentOS base image
FROM centos

USER root

# Fix CentOS repo URLs and update system
RUN sed -i 's|^mirrorlist=|#mirrorlist=|g' /etc/yum.repos.d/CentOS-* && \
    sed -i 's|^#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-* && \
    yum clean all
RUN yum update -y

# Install required packages
RUN yum install gcc make zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel openssh-server openssh-clients libffi-devel wget tar -y && yum clean all

# Install Python 3.9.6
RUN cd /usr/src && \
    wget https://www.python.org/ftp/python/3.9.6/Python-3.9.6.tgz && \
    tar xzf Python-3.9.6.tgz && \
    cd Python-3.9.6 && \
    ./configure --enable-optimizations && \
    make altinstall && \
    ln -s /usr/local/bin/python3.9 /usr/bin/python3

# Configure Python environment
RUN export USER_BASE_PATH=$(python3 -m site --user-base) && \
    echo "export PATH=\$PATH:$USER_BASE_PATH/bin" >> /root/.bashrc

# Install Ansible
RUN python3 -m pip install --user ansible
RUN python3 -m site --user-base

# Configure SSH
COPY id_rsa /root/.ssh/id_rsa
RUN chmod 700 /root/.ssh
RUN chmod 400 /root/.ssh/*

# Configure SSH hosts
RUN printf "Host admin_server\n  HostName 192.168.56.10\n  User vagrant\n  IdentityFile /root/.ssh/id_rsa\n  StrictHostKeyChecking no\n\nHost managed_server\n  HostName 192.168.56.11\n  User vagrant\n  IdentityFile /root/.ssh/id_rsa\n  StrictHostKeyChecking no\n\nHost tomcat_server\n  HostName 192.168.56.12\n  User vagrant\n  IdentityFile /root/.ssh/id_rsa\n  StrictHostKeyChecking no\n" >> /root/.ssh/config

# Set working directory
WORKDIR /ansible

# Configure container startup
CMD ["/bin/bash", "-c", "echo '192.168.56.10 admin_server' >> /etc/hosts && echo '192.168.56.11 managed_server' >> /etc/hosts && echo '192.168.56.12 tomcat_server' >> /etc/hosts && bash"]