# syntax=docker/dockerfile:1

# stack image
ARG STACK_IMAGE="${STACK_IMAGE:-esmf/esmf-build-stack:latest}"
FROM ${STACK_IMAGE}

# build arguments
ARG ESMF_VERSION=${ESMF_VERSION:-8.0.0}

# labels
LABEL description="Build Image for ESMF ${ESMF_VERSION}" \
      version="1.0" \
      org.earthsystemmodeling.esmf.version="${ESMF_VERSION}"

# create user directories
ENV USER="${USER:-dev}"
ENV HOME="${HOME:-/home/${USER}}"
ENV DOWNLOAD_DIR="${DOWNLOAD_DIR:-${HOME}/downloads}"
ENV SOURCE_DIR="${SOURCE_DIR:-${HOME}/source}"
ENV STACK_DIR="${STACK_DIR:-${HOME}/stack}"
RUN <<ADD_DIRECTORIES bash
  mkdir -p ${DOWNLOAD_DIR}
  mkdir -p ${SOURCE_DIR}
  mkdir -p ${STACK_DIR}
ADD_DIRECTORIES

# install esmf
ENV ESMF_VERSION="${ESMF_VERSION}"
ENV ESMF_URL="https://github.com/esmf-org/esmf/archive/refs/tags/v${ESMF_VERSION}.tar.gz"
ENV ESMF_DIR="${SOURCE_DIR}/esmf-${ESMF_VERSION}"
ENV ESMF_ABI="${SYS_ABI}"
ENV ESMF_BOPT="O"
ENV ESMF_COMM="${SYS_COMM}"
ENV ESMF_COMPILER="${SYS_COMPILER}"
ENV ESMF_OS="${SYS_OS}"
ENV ESMF_SITE="default"
ENV ESMF_NETCDF="split"
ENV ESMF_NETCDF_LIBPATH="${NETCDF_DIR}/lib"
ENV ESMF_NETCDF_INCLUDE="${NETCDF_DIR}/include"
ENV ESMF_ARRAY_LITE="FALSE"
ENV ESMF_NO_INTEGER_1_BYTE="TRUE"
ENV ESMF_NO_INTEGER_2_BYTE="TRUE"
ENV ESMF_FORTRANSYMBOLS="default"
ENV ESMF_MAPPER_BUILD="OFF"
ENV ESMF_AUTO_LIB_BUILD="ON"
ENV ESMF_DEFER_LIB_BUILD="ON"
ENV ESMF_SHARED_LIB_BUILD="ON"
ENV ESMF_INSTALL_MODDIR="mod"
ENV ESMF_INSTALL_BINDIR="bin"
ENV ESMF_INSTALL_LIBDIR="lib"
ENV ESMF_INSTALL_PREFIX="${STACK_DIR}/esmf-${ESMF_VERSION}"
RUN <<INSTALL_ESMF bash
  mkdir -p ${ESMF_DIR}
  cd ${ESMF_DIR}
  curl -L ${ESMF_URL} | tar --strip-components=1 -xz
  make all install
  cd ${ESMF_DIR}/src/addon/esmpy
  pip3 install .
  cd ${SOURCE_DIR} && rm -rf ${ESMF_DIR}
INSTALL_ESMF
ENV PATH="${PATH}:${ESMF_INSTALL_PREFIX}/bin"
ENV LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${ESMF_INSTALL_PREFIX}/lib"
ENV ESMFMKFILE="${ESMF_INSTALL_PREFIX}/lib/esmf.mk"

# download prototypes
ENV PROTO_VERSION="${ESMF_VERSION}"
ENV PROTO_URL="https://github.com/esmf-org/nuopc-app-prototypes/archive/refs/tags/v${PROTO_VERSION}.tar.gz"
ENV PROTO_DIR="${SOURCE_DIR}/nuopc-app-prototypes-${PROTO_VERSION}"
RUN <<INSTALL_PROTOTYPES bash
  mkdir -p ${PROTO_DIR}
  cd ${PROTO_DIR}
  curl -L ${PROTO_URL} | tar --strip-components=1 -xz
INSTALL_PROTOTYPES

