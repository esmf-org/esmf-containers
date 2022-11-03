# syntax=docker/dockerfile:1

# base image
FROM ubuntu:20.04

# non interactive mode
ENV DEBIAN_FRONTEND noninteractive

# environment variables passed to image
ENV SYS_ABI="64"
ENV SYS_OS="Linux"
ENV SYS_COMPILER="gfortran"
ENV SYS_COMPILER_VERSION="9"

# labels
LABEL description="Base Image for ESMF Build Environment" \
      version="1.0" \
      org.earthsystemmodeling.system.abi="${SYS_ABI}" \
      org.earthsystemmodeling.system.os="${SYS_OS}" \
      org.earthsystemmodeling.system.compiler="${SYS_COMPILER}" \
      org.earthsystemmodeling.system.compiler.version="${SYS_COMPILER_VERSION}"

# install packages
RUN <<INSTALL_PACKAGES bash
  apt-get update
  ln -fs /usr/share/zoneinfo/America/New_York /etc/localtime
  apt-get install -y tzdata
  dpkg-reconfigure --frontend noninteractive tzdata
  apt-get install -y \
    build-essential \
    gcc-9 \
    gfortran-9 \
    wget \
    curl \
    m4 \
    zlib1g \
    zlib1g-dev \
    libssl-dev \
    python3 \
    python3-pip \
    git \
    vim \
    openssh-client \
    csh
  apt-get clean
  pip3 install PyYAML
  pip3 install numpy
INSTALL_PACKAGES

# system setup
RUN <<SYSTEM_SETUP bash
  echo 'root:password' | chpasswd
  ln -sf /bin/bash /bin/sh
  ln -sf /usr/bin/make /usr/bin/gmake
  ln -sf /usr/bin/python3 /usr/bin/python
SYSTEM_SETUP

# set compilers
ENV CC="gcc-9" \
    CXX="g++-9" \
    F77="gfortran-9" \
    F90="gfortran-9" \
    FC="gfortran-9"

# create users
ENV USER="dev"
ENV HOME="/home/${USER}"
RUN <<ADD_USER bash
  groupadd -r ${USER}
  useradd -r -m -d ${HOME} -s /bin/bash -g ${USER} ${USER}
  echo '${USER}:password' | chpasswd
ADD_USER

USER ${USER}

# default execution command
WORKDIR ${HOME}
SHELL ["/bin/bash","-ec"]
CMD ["/bin/bash","-l"]

