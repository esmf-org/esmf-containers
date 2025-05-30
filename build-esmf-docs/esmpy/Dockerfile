FROM ubuntu:plucky

# install packages
RUN <<INSTALL_PACKAGES bash
  apt-get update
  ln -fs /usr/share/zoneinfo/America/Denver /etc/localtime
  apt-get install -y tzdata
  dpkg-reconfigure --frontend noninteractive tzdata
  apt-get install -y \
    latexmk \
    texlive-fonts-recommended \
    texlive-latex-recommended \
    texlive-latex-extra \
    build-essential \
    gfortran \
    git \
    python3-pip \
    python3-venv
  apt-get clean
INSTALL_PACKAGES

ARG ESMF_REPOSITORY="https://github.com/esmf-org/esmf.git"
ARG ESMF_BRANCH="develop"
RUN echo "ESMF_REPOSITORY=$ESMF_REPOSITORY\n\
          ESMF_BRANCH=$ESMF_BRANCH"

ENV ARTIFACTS=/artifacts/doc-esmpy
RUN mkdir -p ${ARTIFACTS}

WORKDIR /opt/
RUN git clone --branch ${ESMF_BRANCH} ${ESMF_REPOSITORY}

ENV ESMF_DIR=/opt/esmf
ENV ESMF_INSTALL_PREFIX=/usr/local
ENV ESMF_INSTALL_BINDIR=${ESMF_INSTALL_PREFIX}/bin
ENV ESMF_INSTALL_DOCDIR=${ESMF_INSTALL_PREFIX}/doc
ENV ESMF_INSTALL_HEADERDIR=${ESMF_INSTALL_PREFIX}/include
ENV ESMF_INSTALL_LIBDIR=${ESMF_INSTALL_PREFIX}/lib
ENV ESMF_INSTALL_MODDIR=${ESMF_INSTALL_PREFIX}/mod

WORKDIR ${ESMF_DIR}
RUN git fetch --tags https://github.com/esmf-org/esmf.git
RUN make -j 2
RUN make install

ENV ESMFMKFILE=${ESMF_INSTALL_LIBDIR}/esmf.mk

# The system doesn't allow installing packages globally with pip (unless adding "python3
# -m pip config set global.break-system-packages true"), so install in a virtual environment.
WORKDIR /opt/
RUN python3 -m venv --system-site-packages esmpy_venv
ENV PYTHON_VENV_ACTIVATE=/opt/esmpy_venv/bin/activate

RUN . ${PYTHON_VENV_ACTIVATE} && python3 -m pip install sphinx sphinxcontrib-packages sphinxcontrib-bibtex

WORKDIR ${ESMF_DIR}/src/addon/esmpy
RUN . ${PYTHON_VENV_ACTIVATE} && python3 -m pip install .
WORKDIR doc
RUN . ${PYTHON_VENV_ACTIVATE} && make html latexpdf

RUN cp -r esmpy_doc ${ARTIFACTS} && \
    cd /artifacts && \
    zip -r doc-esmpy.zip doc-esmpy
