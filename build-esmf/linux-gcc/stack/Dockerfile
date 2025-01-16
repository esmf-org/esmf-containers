# syntax=docker/dockerfile:1

# base image
ARG BASE_IMAGE="esmf/esmf-build-base:latest"
FROM ${BASE_IMAGE}

# labels
LABEL description="Stack Image by ESMF Build Environment" \
      version="2.0" \
      org.earthsystemmodeling.stack.compiler="${SYS_COMPILER}" \
      org.earthsystemmodeling.stack.comm="openmpi"

# install software stack
RUN <<INSTALL_STACK bash -e
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack install lmod@8.7.24
  spack install cmake@3.24.3
  spack install openmpi@4.1.6%gcc
  spack install hdf5@1.12.2+fortran
  spack install netcdf-c@4.7.4
  spack install netcdf-fortran@4.4.5
  spack gc -y
  spack clean --all
INSTALL_STACK

# setup modules
RUN <<SETUP_MODULES bash -e
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack config add "modules:default:enable:[tcl]"
  spack config add "modules:default:tcl:hash_length:0"
  spack config add "modules:default:tcl:all:environment:set:'{name}_ROOT':'{prefix}'"
  spack config add "modules:default:tcl:openmpi:environment:set:CC:mpicc"
  spack config add "modules:default:tcl:openmpi:environment:set:CXX:mpicxx"
  spack config add "modules:default:tcl:openmpi:environment:set:F77:mpif77"
  spack config add "modules:default:tcl:openmpi:environment:set:F90:mpif90"
  spack config add "modules:default:tcl:openmpi:environment:set:FC:mpifort"
  spack config add "modules:default:tcl:netcdf-c:environment:append_path:LD_LIBRARY_PATH:'{prefix}/lib'"
  spack config add "modules:default:tcl:netcdf-fortran:environment:append_path:LD_LIBRARY_PATH:'{prefix}/lib'"
  spack module tcl refresh --delete-tree -y
SETUP_MODULES

# set environment
ENV MODULES_DEFAULT="cmake openmpi hdf5 netcdf-c netcdf-fortran" \
    STACK_COMPILER="${SYS_COMPILER}" \
    STACK_COMM="openmpi"
