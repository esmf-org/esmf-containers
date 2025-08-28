# syntax=docker/dockerfile:1

# base image
ARG BASE_IMAGE="esmf/esmf-build-base:latest"
FROM ${BASE_IMAGE}

# labels
LABEL description="Stack Image by ESMF Build Environment" \
      version="3.0" \
      org.earthsystemmodeling.stack.compiler="${SYS_COMPILER}" \
      org.earthsystemmodeling.stack.comm="openmpi"

# install software stack
RUN <<INSTALL_STACK
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack env activate ${SPACK_ENV_DEFAULT}
  spack add cmake@3.31
  spack add lmod@8.7
  spack add hdf5@1.14+fortran
  spack add netcdf-c@4.9
  spack add netcdf-fortran@4.6
  spack add openmpi@5.0
  spack add vim@9.1
  spack concretize
  spack install --fail-fast
  spack config add "modules:default:enable:[tcl]"
  spack config add "modules:default:tcl:exclude_implicits:true"
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
  . $(spack location -i lmod)/lmod/lmod/init/bash
  . ${SPACK_ROOT}/share/spack/setup-env.sh
  spack env activate ${SPACK_ENV_DEFAULT}
  spack gc -y
  spack clean --all
INSTALL_STACK

# set environment
ENV MODULES_DEFAULT="cmake openmpi hdf5 netcdf-c netcdf-fortran vim" \
    STACK_COMPILER="${SYS_COMPILER}" \
    STACK_COMM="openmpi"
