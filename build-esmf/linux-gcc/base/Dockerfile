# syntax=docker/dockerfile:1

# base image
FROM ubuntu:24.04

# information passed to image
ARG SYS_ABI="64" \
    SYS_OS="Linux" \
    SYS_COMPILER="gfortran" \
    SYS_COMPILER_VERSION="13"

# labels
LABEL description="Base Image for ESMF Build Environment" \
      version="2.0" \
      org.earthsystemmodeling.system.abi="${SYS_ABI}" \
      org.earthsystemmodeling.system.os="${SYS_OS}" \
      org.earthsystemmodeling.system.compiler="${SYS_COMPILER}" \
      org.earthsystemmodeling.system.compiler.version="${SYS_COMPILER_VERSION}"

# install packages
RUN <<INSTALL_PACKAGES bash -e
  apt-get update
  ln -fs /usr/share/zoneinfo/America/New_York /etc/localtime
  apt-get install -y tzdata
  dpkg-reconfigure --frontend noninteractive tzdata
  apt-get install -y \
    build-essential \
    ca-certificates \
    coreutils \
    environment-modules \
    gcc-13 \
    gfortran-13 \
    gdb \
    gpg \
    wget \
    curl \
    libssl-dev \
    lsb-release \
    python3 \
    python3-pip \
    python3-venv \
    git \
    vim \
    unzip \
    zip
  apt-get clean
INSTALL_PACKAGES

# system setup
RUN <<SYSTEM_SETUP bash -e
  echo 'root:password' | chpasswd
  ln -sf /bin/bash /bin/sh
  ln -sf /usr/bin/gcc-13 /usr/bin/gcc
  ln -sf /usr/bin/gfortran-13 /usr/bin/gfortran
  ln -sf /usr/bin/make /usr/bin/gmake
  ln -sf /usr/bin/python3 /usr/bin/python
SYSTEM_SETUP

# create users
ARG USER="dev" \
    HOME="/home/dev"
ARG SOURCE_DIR="${HOME}/source" \
    INSTALL_DIR="${HOME}/install" \
    EXAMPLES_DIR="${HOME}/examples" \
    SPACK_ROOT="${HOME}/spack"
RUN <<ADD_USER bash -e
  groupadd -r ${USER}
  useradd -r -m -d ${HOME} -s /bin/bash -g ${USER} ${USER}
  echo '${USER}:password' | chpasswd
ADD_USER

USER ${USER}
WORKDIR ${HOME}

# create user directories
RUN <<ADD_DIRECTORIES bash -e
  mkdir -p ${SOURCE_DIR}
  mkdir -p ${INSTALL_DIR}
  mkdir -p ${EXAMPLES_DIR}
  mkdir -p ${SPACK_ROOT}
ADD_DIRECTORIES

# install python packages for user
RUN <<INSTALL_PYTHON_PKGS bash -e
  python3 -m pip install --break-system-packages PyYAML
  python3 -m pip install --break-system-packages numpy
INSTALL_PYTHON_PKGS

# spack v0.21.1 (2024-01-11) does not work with esmf version 8.6.0+
# using spack tag: develop-2024-01-21
# version: 02dc10c0532fc36f36d1c3e2d1feb6d2b0e57b8a
RUN <<INSTALL_SPACK bash -e
  git clone --depth=1 --branch=develop-2024-01-21 https://github.com/spack/spack.git ${SPACK_ROOT}
INSTALL_SPACK

RUN <<SETUP_SPACK bash -e
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
