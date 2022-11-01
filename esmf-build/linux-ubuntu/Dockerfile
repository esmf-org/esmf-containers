# syntax=docker/dockerfile:1

ARG BASE_VERSION=latest

# base image
FROM esmf/esmf-buildenv-ubuntu:${BASE_VERSION}
ARG ESMF_VERSION=${ESMF_VERSION:-8.0.0}

# labels
LABEL description="Image for ESMF Build ${ESMF_VERSION}"
LABEL version="1.0"
LABEL "org.earthsystemmodeling.esmf.version"=${ESMF_VERSION}

USER dev

# environment
ENV DEVHOME=/home/dev
ARG DEVDNL=${DEVHOME}/downloads
ARG DEVSRC=${DEVHOME}/source
ARG DEVSTK=${DEVHOME}/stack

# install esmf
ENV ESMF_VERSION=${ESMF_VERSION}
ARG ESMF_URL=https://github.com/esmf-org/esmf/archive/refs/tags/v${ESMF_VERSION}.tar.gz
ARG ESMF_TAR=esmf-${ESMF_VERSION}.tar.gz
ARG ESMF_DIR=${DEVSRC}/esmf-${ESMF_VERSION}
ENV ESMF_INSTALL_PREFIX="${DEVSTK}/esmf-${ESMF_VERSION}"
WORKDIR ${ESMF_DIR}
ADD --chown=dev:dev ${ESMF_URL} ${DEVDNL}/${ESMF_TAR}
RUN <<INSTALL_ESMF bash
  tar --strip-components=1 -xzf ${DEVDNL}/${ESMF_TAR}
  make all install
  cd ${DEVSRC} && rm -rf esmf-${ESMF_VERSION}
INSTALL_ESMF
ENV PATH="${PATH}:${ESMF_INSTALL_PREFIX}/bin"
ENV LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${ESMF_INSTALL_PREFIX}/lib"
ENV ESMFMKFILE="${ESMF_INSTALL_PREFIX}/lib/esmf.mk"

# default execution command
WORKDIR ${DEVHOME}
SHELL ["/bin/bash","-ec"]
CMD ["/bin/bash","-l"]
