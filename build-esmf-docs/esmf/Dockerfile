########################################
# Build and collect ESMF documentation #
########################################

FROM esmf/build-esmf-docs-base:2022-07-28

ARG ESMF_REPOSITORY="https://github.com/esmf-org/esmf.git"
ARG ESMF_BRANCH="develop"
RUN echo "ESMF_REPOSITORY=$ESMF_REPOSITORY\n\
          ESMF_BRANCH=$ESMF_BRANCH"

# Where documentation artifacts will be stored in the container
ENV ESMF_ARTIFACTS=/artifacts
RUN mkdir -p ${ESMF_ARTIFACTS}

# Clone ESMF
WORKDIR ${HOME}/sandbox/esmf
RUN git clone ${ESMF_REPOSITORY} src-git

ENV ESMF_DIR=${HOME}/sandbox/esmf/src-git

# Fetch tags
WORKDIR ${ESMF_DIR}
RUN git checkout --detach && git fetch origin ${ESMF_BRANCH}:${ESMF_BRANCH} && git checkout ${ESMF_BRANCH} && git fetch --tags https://github.com/esmf-org/esmf.git

# Make the NUOPC documentation
WORKDIR ${ESMF_DIR}/src/addon/NUOPC/doc
RUN make localdoc

# Make the ESMF documentation
WORKDIR ${ESMF_DIR}
RUN make doc

# Make the ESMF dev guide documentation
WORKDIR ${ESMF_DIR}/src/doc/dev_guide
RUN make

# Collect and compress the documentation artifacts
WORKDIR ${ESMF_DIR}
RUN cp -rv doc ${ESMF_ARTIFACTS}/doc-esmf
RUN mkdir ${ESMF_ARTIFACTS}/doc-dvi
RUN cp -v src/doc/*.dvi src/addon/NUOPC/doc/*.dvi ${ESMF_ARTIFACTS}/doc-dvi
RUN mkdir ${ESMF_ARTIFACTS}/doc-nuopc
RUN cp -rv src/addon/NUOPC/doc/*.pdf src/addon/NUOPC/doc/NUOPC_refdoc src/addon/NUOPC/doc/NUOPC_howtodoc ${ESMF_ARTIFACTS}/doc-nuopc
RUN mkdir ${ESMF_ARTIFACTS}/doc-dev_guide
RUN cp -rv src/doc/dev_guide ${ESMF_ARTIFACTS}/doc-dev_guide

RUN zip -r ${ESMF_ARTIFACTS}/doc-artifacts.zip ${ESMF_ARTIFACTS}
