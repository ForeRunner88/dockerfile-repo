# ARG for non-noninteractive
ARG DEBIAN_FRONTEND=noninteractive
ARG DEBIAN_PRIORITY=critical
# ARG for default user
ARG user

FROM ubuntu:16.04
LABEL \
    COPYRIGHT="Copyright 2019 Allen Wu, all rights reserved." \
    maintainer="Allen Wu <wx313426784@163.com>" \
    version="v1.0" \
    description="Build for common developer environment."

ENV TERM=xterm-color
ENV DEFAULT_USER=${user:-'developer'}
ENV HOME=/home/${DEFAULT_USER}
ENV PATH=${HOME}/.local/bin:${PATH}

# Replace sources
RUN cp /etc/apt/sources.list /etc/apt/sources.list.old && \
    sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list && \
    sed -i 's/security.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list && \
    # needed to be able to install resolvconf without complaints about /etc/resolv.conf not being accessible
    # echo "resolvconf resolvconf/linkify-resolvconf boolean false" | debconf-set-selections && \
    dpkg --add-architecture i386 && \
    apt-get -y update && \
    apt-get -y install \
        apt-utils \
        locales \
        tzdata \
        bash-completion \
        sudo \
        ca-certificates \
        apt-transport-https \
        resolvconf \
        expect \
        kmod \
        policykit-1 \
        psmisc \
        lsof \
        lsb-core \
        udev \
        cpio \
        pkg-config \
        unzip \
        xterm \
        tmux \
        htop \
        tree

# Open bash-completion
RUN echo "if [ -f /etc/bash_completion ] && ! shopt -oq posix; then" >> /root/.bashrc && \
    echo "    . /etc/bash_completion" >> /root/.bashrc && \
    echo "fi" >> /root/.bashrc


# Set up time and locale language
RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    locale-gen && \
    ln -snf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

# Create user who can run as sudoer with no passwd.
RUN export uid=1000 gid=1000 && \
    mkdir -p ${HOME} \
    cp /root/.bashrc /root/.profile ${HOME}/ && \
    echo "${DEFAULT_USER}:x:${uid}:${gid}:${DEFAULT_USER},,,:${HOME}:/bin/bash" >> /etc/passwd && \
    echo "${DEFAULT_USER}:x:${uid}:" >> /etc/group && \
    echo "${DEFAULT_USER} ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/${DEFAULT_USER} && \
    chmod 0440 /etc/sudoers.d/${DEFAULT_USER} && \
    chown ${uid}:${gid} -R ${HOME}

# Swtich user
USER ${DEFAULT_USER}
WORKDIR ${HOME}

# Install packages
RUN sudo apt-get -y install \
        cmake \
        build-essential \
        gcc-multilib \
        git \
        nfs-kernel-server \
        isc-dhcp-server \
        openssh-server \
        dnsutils \
        iputils-ping \
        gnupg-curl \
        curl \
        libtiff5-dev \
        libjpeg-dev \
        libpng12-dev \
        libjasper-dev \
        libavcodec-dev \
        libavformat-dev \
        libavutil-dev  \
        libpostproc-dev \
        libswscale-dev


# Clean up cache
RUN sudo apt-get -y autoremove && \
    sudo apt-get -y clean && \
    sudo rm -rf /var/lib/apt/lists/* && \
    sudo rm -rf /etc/apt/sources.list.d/*
