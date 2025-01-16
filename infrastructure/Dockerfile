FROM centos

USER root

RUN sed -i 's|^mirrorlist=|#mirrorlist=|g' /etc/yum.repos.d/CentOS-* && \
    sed -i 's|^#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-* && \
    yum clean all
RUN yum update -y
RUN yum install gcc make zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel openssh-server openssh-clients libffi-devel wget tar -y && yum clean all

RUN cd /usr/src && \
    wget https://www.python.org/ftp/python/3.9.6/Python-3.9.6.tgz && \
    tar xzf Python-3.9.6.tgz && \
    cd Python-3.9.6 && \
    ./configure --enable-optimizations && \
    make altinstall && \
    ln -s /usr/local/bin/python3.9 /usr/bin/python3

RUN export USER_BASE_PATH=$(python3 -m site --user-base) && \
    echo "export PATH=\$PATH:$USER_BASE_PATH/bin" >> /root/.bashrc

RUN python3 -m pip install --user ansible
RUN python3 -m site --user-base

COPY id_rsa /root/.ssh/id_rsa
RUN chmod 700 /root/.ssh
RUN chmod 400 /root/.ssh/*

RUN printf "Host admin_server\n  HostName 192.168.56.10\n  User vagrant\n  IdentityFile /root/.ssh/id_rsa\n  StrictHostKeyChecking no\n\nHost managed_server\n  HostName 192.168.56.11\n  User vagrant\n  IdentityFile /root/.ssh/id_rsa\n  StrictHostKeyChecking no\n" >> /root/.ssh/config

WORKDIR /ansible

CMD ["bash"]