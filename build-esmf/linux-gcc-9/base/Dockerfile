# syntax=docker/dockerfile:1

# base image
FROM ubuntu:20.04

# information passed to image
ARG SYS_ABI="64"
ARG SYS_OS="Linux"
ARG SYS_COMPILER="gfortran"
ARG SYS_COMPILER_VERSION="9"

# labels
LABEL description="Base Image for ESMF Build Environment" \
      version="2.0" \
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
    ca-certificates \
    coreutils \
    environment-modules \
    gcc-9 \
    gfortran-9 \
    gdb \
    gpg \
    wget \
    curl \
    libssl-dev \
    lsb-release \
    python3 \
    python3-distutils \
    python3-pip \
    python3-venv \
    git \
    vim \
    unzip \
    zip
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

# create users
ARG USER="${USER:-dev}"
ARG HOME="${HOME:-/home/${USER}}"
ARG SOURCE_DIR="${SOURCE_DIR:-${HOME}/source}" \
    INSTALL_DIR="${INSTALL_DIR:-${HOME}/install}" \
    EXAMPLES_DIR="${EXAMPLES_DIR:-${HOME}/examples}" \
    SPACK_ROOT="${SPACK_ROOT:-${HOME}/spack}"
RUN <<ADD_USER bash
  groupadd -r ${USER}
  useradd -r -m -d ${HOME} -s /bin/bash -g ${USER} ${USER}
  echo '${USER}:password' | chpasswd
ADD_USER

USER ${USER}
WORKDIR ${HOME}

# create user directories
RUN <<ADD_DIRECTORIES bash
  mkdir -p ${DOWNLOAD_DIR}
  mkdir -p ${SOURCE_DIR}
  mkdir -p ${INSTALL_DIR}
  mkdir -p ${EXAMPLES_DIR}
  mkdir -p ${SPACK_ROOT}
ADD_DIRECTORIES

# spack v0.21.1 (2024-01-11) does not work with esmf version 8.6.0+
# using spack tag: develop-2024-01-21
# version: 02dc10c0532fc36f36d1c3e2d1feb6d2b0e57b8a
RUN <<INSTALL_SPACK bash
  git clone --depth=1 --branch=develop-2024-01-21 https://github.com/spack/spack.git ${SPACK_ROOT}
INSTALL_SPACK

RUN <<SETUP_SPACK bash
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack compiler find
  spack external find openssl
  spack config add "packages:openssl:buildable:False"
SETUP_SPACK

# set environment
ENV USER="${USER}" \
    HOME="${HOME}" \
    SOURCE_DIR="${SOURCE_DIR}" \
    INSTALL_DIR="${INSTALL_DIR}" \
    EXAMPLES_DIR="${EXAMPLES_DIR}" \
    SPACK_ROOT="${SPACK_ROOT}" \
    SYS_ABI="${SYS_ABI}" \
    SYS_OS="${SYS_OS}" \
    SYS_COMPILER="${SYS_COMPILER}" \
    SYS_COMPILER_VERSION="${SYS_COMPILER_VERSION}"

# default execution command
COPY --chown=dev:dev bashrc /home/dev/.bashrc
ENTRYPOINT ["/bin/bash", "-l"]
