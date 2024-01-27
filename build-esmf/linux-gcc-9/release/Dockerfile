# syntax=docker/dockerfile:1

# stack image
ARG STACK_IMAGE="${STACK_IMAGE:-esmf/esmf-build-stack:latest}"
FROM ${STACK_IMAGE}

# build arguments
ARG ESMF_VERSION=${ESMF_VERSION:-8.0.0}

# labels
LABEL description="Build Image for ESMF ${ESMF_VERSION}" \
      version="2.0" \
      org.earthsystemmodeling.esmf.version="${ESMF_VERSION}"

# install esmf
RUN <<INSTALL_ESMF bash
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack env activate ${SPACK_ENV_DEFAULT}
  spack install --add esmf@${ESMF_VERSION}~external-parallelio~pnetcdf
  spack gc -y
  spack clean --all
INSTALL_ESMF

# install esmpy
ARG ESMF_URL="https://github.com/esmf-org/esmf/archive/refs/tags/v${ESMF_VERSION}.tar.gz"
ARG ESMF_DIR="${SOURCE_DIR}/esmf-${ESMF_VERSION}"
RUN <<INSTALL_ESMPY bash
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack env activate ${SPACK_ENV_DEFAULT}
  mkdir -p ${ESMF_DIR}
  cd ${ESMF_DIR}
  curl -L ${ESMF_URL} | tar --strip-components=1 -xz
  cd ${ESMF_DIR}/src/addon/esmpy
  pip3 install .
  cd ${SOURCE_DIR} && rm -rf ${ESMF_DIR}
INSTALL_ESMPY

# download prototypes
ARG PROTO_URL="https://github.com/esmf-org/nuopc-app-prototypes/archive/refs/tags/v${ESMF_VERSION}.tar.gz"
ARG PROTO_DIR="${EXAMPLES_DIR}/nuopc-app-prototypes-${ESMF_VERSION}"
RUN <<INSTALL_PROTOTYPES bash
  mkdir -p ${PROTO_DIR}
  cd ${PROTO_DIR}
  curl -L ${PROTO_URL} | tar --strip-components=1 -xz
INSTALL_PROTOTYPES

ENV ESMF_VERSION="${ESMF_VERSION}" \
    MODULES_DEFAULT="esmf ${MODULES_DEFAULT}"
