# syntax=docker/dockerfile:1

# base image
FROM esmf/esmf-buildenv-ubuntu:20.04
ARG ESMF_VERSION=${ESMF_VERSION:-8.0.0}

# labels
LABEL description="Image for ESMF Build ${ESMF_VERSION}"
LABEL version="1.0"
LABEL "org.earthsystemmodeling.esmf.version"=${ESMF_VERSION}

USER dev

# environment
ENV DEVHOME=/home/dev
ENV DEVDNL=${DEVHOME}/downloads
ENV DEVSRC=${DEVHOME}/source
ENV DEVSTK=${DEVHOME}/stack

# install esmf
ENV ESMF_VERSION=${ESMF_VERSION}
ENV ESMF_URL=https://github.com/esmf-org/esmf/archive/refs/tags/v${ESMF_VERSION}.tar.gz
ENV ESMF_DIR=${DEVSRC}/esmf-${ESMF_VERSION}
ENV ESMF_INSTALL_PREFIX="${DEVSTK}/esmf-${ESMF_VERSION}"
RUN <<INSTALL_ESMF bash
  mkdir -p ${ESMF_DIR}
  cd ${ESMF_DIR}
  wget ${ESMF_URL} -O esmf-${ESMF_VERSION}.tar.gz
  tar --strip-components=1 -xzf esmf-${ESMF_VERSION}.tar.gz
  make all install
  cd ${DEVSRC} && rm -rf ${ESMF_DIR}
INSTALL_ESMF
ENV PATH="${PATH}:${ESMF_INSTALL_PREFIX}/bin"
ENV LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${ESMF_INSTALL_PREFIX}/lib"
ENV ESMFMKFILE="${ESMF_INSTALL_PREFIX}/lib/esmf.mk"

# download prototypes
ENV PROTO_VERSION=${ESMF_VERSION}
ARG PROTO_URL=https://github.com/esmf-org/nuopc-app-prototypes/archive/refs/tags/v${PROTO_VERSION}.tar.gz
ENV PROTO_DIR=${DEVSRC}/nuopc-app-prototypes-${PROTO_VERSION}
RUN <<INSTALL_PROTOTYPES bash
  mkdir -p ${PROTO_DIR}
  cd ${PROTO_DIR}
  wget ${PROTO_URL} -O nuopc-app-prototypes-${PROTO_VERSION}.tar.gz
  tar --strip-components=1 -xzf nuopc-app-prototypes-${PROTO_VERSION}.tar.gz
  rm -f nuopc-app-prototypes-${PROTO_VERSION}.tar.gz
INSTALL_PROTOTYPES

# default execution command
WORKDIR ${DEVHOME}
SHELL ["/bin/bash","-ec"]
CMD ["/bin/bash","-l"]
