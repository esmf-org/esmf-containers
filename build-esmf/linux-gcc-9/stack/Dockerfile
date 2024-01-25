# syntax=docker/dockerfile:1

# base image
ARG BASE_IMAGE="${BASE_IMAGE:-esmf/esmf-build-base:latest}"
FROM ${BASE_IMAGE}

# labels
LABEL description="Stack Image by ESMF Build Environment" \
      version="2.0" \
      org.earthsystemmodeling.stack.compiler="${SYS_COMPILER}" \
      org.earthsystemmodeling.stack.comm="openmpi"

# install software stack into environment
ARG SPACK_ENV_DEFAULT="${SPACK_ENV_DEFAULT:-gfortran-openmpi}"
RUN <<SETUP_STACK bash
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack env create ${SPACK_ENV_DEFAULT}
  spack env activate ${SPACK_ENV_DEFAULT}
  spack add cmake@3.24.3
  spack add openmpi@4.1.6%gcc
  spack add hdf5@1.12.2+fortran
  spack add netcdf-c@4.7.4
  spack add netcdf-fortran@4.4.5
  spack concretize
  spack install
  spack gc -y
  spack clean --all
SETUP_STACK

# set environment
ENV SPACK_ENV_DEFAULT="${SPACK_ENV_DEFAULT}" \
    STACK_COMPILER="${SYSTEM_COMPILER}" \
    STACK_COMM="openmpi"
