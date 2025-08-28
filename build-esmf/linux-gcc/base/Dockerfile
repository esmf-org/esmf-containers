# syntax=docker/dockerfile:1

# base image - Ubuntu Noble Numbat LTS (2024-04-25)
FROM ubuntu:24.04

# information passed to image
ARG SYS_ABI="64" \
    SYS_OS="Linux" \
    SYS_COMPILER="gfortran" \
    SYS_COMPILER_VERSION="13"

# labels
LABEL description="Base Image for ESMF Build Environment" \
      version="3.0" \
      org.earthsystemmodeling.system.abi="${SYS_ABI}" \
      org.earthsystemmodeling.system.os="${SYS_OS}" \
      org.earthsystemmodeling.system.compiler="${SYS_COMPILER}" \
      org.earthsystemmodeling.system.compiler.version="${SYS_COMPILER_VERSION}"

# shell setup
SHELL ["/bin/bash", "-ec"]

# install packages
RUN <<INSTALL_PACKAGES
  apt-get update
  apt-get install -yqq \
    build-essential \
    pkg-config \
    file \
    bzip2 \
    ca-certificates \
    g++-13 \
    gcc-13 \
    gfortran-13 \
    git \
    gzip \
    lsb-release \
    patch \
    python3 \
    tar \
    unzip \
    xz-utils \
    zstd
  apt-get clean
  rm -rf /var/lib/apt/lists/*
INSTALL_PACKAGES

# system setup
RUN <<SYSTEM_SETUP
  echo "root:password" | chpasswd
  ln -sf /bin/bash /bin/sh
  ln -sf /usr/bin/g++-13 /usr/bin/g++
  ln -sf /usr/bin/gcc-13 /usr/bin/gcc
  ln -sf /usr/bin/gfortran-13 /usr/bin/gfortran
  ln -sf /usr/bin/python3 /usr/bin/python
SYSTEM_SETUP

# create users
ARG USER="dev" \
    HOME="/home/dev"
ARG SOURCE_DIR="${HOME}/source" \
    INSTALL_DIR="${HOME}/install" \
    EXAMPLES_DIR="${HOME}/examples" \
    SPACK_ROOT="${HOME}/spack" \
    SPACK_ENV_DEFAULT="esmf"
RUN <<ADD_USER
  groupadd -r ${USER}
  useradd -r -m -d ${HOME} -s /bin/bash -g ${USER} ${USER}
  echo "${USER}:password" | chpasswd
ADD_USER

USER ${USER}
WORKDIR ${HOME}

# create user directories
RUN <<ADD_DIRECTORIES
  mkdir -p ${SOURCE_DIR}
  mkdir -p ${INSTALL_DIR}
  mkdir -p ${EXAMPLES_DIR}
  mkdir -p ${SPACK_ROOT}
ADD_DIRECTORIES

# spack v1.0.1 (2025-08-11)
RUN <<INSTALL_SPACK
  git clone --depth=2 --branch=releases/v1.0 https://github.com/spack/spack.git ${SPACK_ROOT}
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack env create ${SPACK_ENV_DEFAULT}
  spack env activate ${SPACK_ENV_DEFAULT}
  spack compiler find
  spack gc -y
  spack clean --all
INSTALL_SPACK

# set environment
ENV USER="${USER}" \
    HOME="${HOME}" \
    SOURCE_DIR="${SOURCE_DIR}" \
    INSTALL_DIR="${INSTALL_DIR}" \
    EXAMPLES_DIR="${EXAMPLES_DIR}" \
    SPACK_ROOT="${SPACK_ROOT}" \
    SPACK_ENV_DEFAULT="${SPACK_ENV_DEFAULT}" \
    SYS_ABI="${SYS_ABI}" \
    SYS_OS="${SYS_OS}" \
    SYS_COMPILER="${SYS_COMPILER}" \
    SYS_COMPILER_VERSION="${SYS_COMPILER_VERSION}"

# default execution command
COPY --chown=dev:dev bashrc /home/dev/.bashrc
ENTRYPOINT ["/bin/bash", "-l"]
