# syntax=docker/dockerfile:1

# stack image
ARG STACK_IMAGE="${STACK_IMAGE:-esmf/esmf-build-stack:latest}"
FROM ${STACK_IMAGE}

# build arguments
ARG ESMF_VERSION=${ESMF_VERSION:-8.9.0}

# labels
LABEL description="Build Image for ESMF ${ESMF_VERSION}" \
      version="3.0" \
      org.earthsystemmodeling.esmf.version="${ESMF_VERSION}"

# install esmf
RUN <<INSTALL_ESMF
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack env activate ${SPACK_ENV_DEFAULT}
  spack add esmf@=${ESMF_VERSION}~external-parallelio~pnetcdf+python
  spack install --no-checksum
  spack gc -y
  spack clean --all
INSTALL_ESMF

# download prototypes
ARG PROTO_URL="https://github.com/esmf-org/nuopc-app-prototypes/archive/refs/tags/v${ESMF_VERSION}.tar.gz"
ARG PROTO_DIR="${EXAMPLES_DIR}/nuopc-app-prototypes-${ESMF_VERSION}"
RUN <<INSTALL_PROTOTYPES
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack env activate ${SPACK_ENV_DEFAULT}
  mkdir -p ${PROTO_DIR}
  cd ${PROTO_DIR}
  curl -L ${PROTO_URL} | tar --strip-components=1 -xz
INSTALL_PROTOTYPES

ENV ESMF_VERSION="${ESMF_VERSION}" \
    MODULES_DEFAULT="esmf ${MODULES_DEFAULT}"
