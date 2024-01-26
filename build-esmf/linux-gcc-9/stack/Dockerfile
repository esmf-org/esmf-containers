# syntax=docker/dockerfile:1

# base image
ARG BASE_IMAGE="${BASE_IMAGE:-esmf/esmf-build-base:latest}"
FROM ${BASE_IMAGE}

# labels
LABEL description="Stack Image by ESMF Build Environment" \
      version="2.0" \
      org.earthsystemmodeling.stack.compiler="${SYS_COMPILER}" \
      org.earthsystemmodeling.stack.comm="openmpi"

# setup spack
ARG SPACK_ENV_DEFAULT="${SPACK_ENV_DEFAULT:-gfortran-openmpi}"
RUN <<SETUP_SPACK_ENV bash -x
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack env create ${SPACK_ENV_DEFAULT}
  spack env activate ${SPACK_ENV_DEFAULT}
  spack config add "modules:default:enable:[tcl]"
  spack config add "modules:default:tcl:hash_length:0"
  spack config add "modules:default:tcl:all:environment:set:'{name}_ROOT':'{prefix}'"
  spack config add "modules:default:tcl:openmpi:environment:set:CC:mpicc"
  spack config add "modules:default:tcl:openmpi:environment:set:CXX:mpicxx"
  spack config add "modules:default:tcl:openmpi:environment:set:F77:mpif77"
  spack config add "modules:default:tcl:openmpi:environment:set:F90:mpif90"
  spack config add "modules:default:tcl:openmpi:environment:set:FC:mpifort"
  spack install --add lmod@8.7.24
SETUP_SPACK_ENV

# install software stack into environment
RUN <<INSTALL_STACK bash
  . ${SPACK_ROOT}/share/spack/setup-env.sh
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
  spack module tcl refresh --delete-tree -y
INSTALL_STACK

# set environment
ENV SPACK_ENV_DEFAULT="${SPACK_ENV_DEFAULT}" \
    MODULES_DEFAULT="openmpi" \
    STACK_COMPILER="${SYSTEM_COMPILER}" \
    STACK_COMM="openmpi"
