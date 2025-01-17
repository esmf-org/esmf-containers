########################################
# Build and collect ESMF documentation #
########################################

FROM esmf/build-esmf-docs-base

ARG ESMF_BRANCH="develop"
RUN echo "ESMF_BRANCH=$ESMF_BRANCH"

# Where documentation artifacts will be stored in the container
ENV ESMF_ARTIFACTS=/artifacts
RUN mkdir -p ${ESMF_ARTIFACTS}

# Clone ESMF
WORKDIR ${HOME}/sandbox/esmf
RUN git clone --branch ${ESMF_BRANCH} https://github.com/esmf-org/esmf.git src-git

ENV ESMF_DIR=${HOME}/sandbox/esmf/src-git

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
RUN cp -v src/doc/*.dvi src/addon/NUOPC/doc/*.dvi ${ESMF_ARTIFACTS}/doc-div
RUN mkdir ${ESMF_ARTIFACTS}/doc-nuopc
RUN cp -rv src/addon/NUOPC/doc/*.pdf src/addon/NUOPC/doc/NUOPC_refdoc src/addon/NUOPC/doc/NUOPC_howtodoc ${ESMF_ARTIFACTS}/doc-nuopc
RUN mkdir ${ESMF_ARTIFACTS}/doc-dev_guide
RUN cp -rv src/doc/dev_guide ${ESMF_ARTIFACTS}/doc-dev_guide

RUN zip -r ${ESMF_ARTIFACTS}/doc-artifacts.zip ${ESMF_ARTIFACTS}
