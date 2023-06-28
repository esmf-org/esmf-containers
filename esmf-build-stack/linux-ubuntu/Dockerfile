# syntax=docker/dockerfile:1

# base image
ARG BASE_IMAGE="${BASE_IMAGE:-esmf/esmf-build-base:latest}"
FROM ${BASE_IMAGE}

# environment variables passed to image
ENV SYS_COMM="openmpi"
ENV SYS_COMM_VERSION="3.0.0"

# labels
LABEL description="Stack Image for ESMF Build Environment" \
      version="1.0" \
      org.earthsystemmodeling.system.comm="${SYS_COMM}" \
      org.earthsystemmodeling.system.comm.version="${SYS_COMM_VERSION}"

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

# install cmake
ENV CMAKE_VERSION="3.24.3"
ENV CMAKE_URL="https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}.tar.gz"
ENV CMAKE_DIR="${STACK_DIR}/cmake-${CMAKE_VERSION}"
RUN <<INSTALL_CMAKE bash
  mkdir -p ${SOURCE_DIR}/cmake-${CMAKE_VERSION}
  cd ${SOURCE_DIR}/cmake-${CMAKE_VERSION}
  curl -L ${CMAKE_URL} | tar --strip-components=1 -xz
  ./bootstrap --prefix=${CMAKE_DIR}
  make all install
  cd ${SOURCE_DIR} && rm -rf cmake-${CMAKE_VERSION}
INSTALL_CMAKE
ENV PATH="${PATH}:${CMAKE_DIR}/bin"
ENV CMAKE="${CMAKE_DIR}/bin/cmake"
ENV CTEST="${CMAKE_DIR}/bin/ctest"

# install openmpi
ENV OPENMPI_VERSION="3.0.0"
ENV OPENMPI_URL="https://download.open-mpi.org/release/open-mpi/v3.0/openmpi-${OPENMPI_VERSION}.tar.gz"
ENV OPENMPI_DIR="${STACK_DIR}/openmpi-${OPENMPI_VERSION}"
RUN <<INSTALL_OPENMPI bash
  mkdir -p ${SOURCE_DIR}/openmpi-${OPENMPI_VERSION}
  cd ${SOURCE_DIR}/openmpi-${OPENMPI_VERSION}
  curl -L ${OPENMPI_URL} | tar --strip-components=1 -xz
  ./configure --enable-fortran --prefix=${OPENMPI_DIR}
  make all install
  cd ${SOURCE_DIR} && rm -rf openmpi-${OPENMPI_VERSION}
INSTALL_OPENMPI
ENV PATH="${PATH}:${OPENMPI_DIR}/bin"
ENV LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${OPENMPI_DIR}/lib"

# set compilers
ENV CC="mpicc"
ENV CXX="mpicxx"
ENV F77="mpif77"
ENV F90="mpif90"
ENV FC="mpifort"

# install hdf5
ENV HDF5_VERSION="1.12.2"
ENV HDF5_URL="https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.12/hdf5-1.12.2/src/hdf5-${HDF5_VERSION}.tar.gz"
ENV HDF5_DIR="${STACK_DIR}/hdf5-${HDF5_VERSION}"
RUN <<INSTALL_HDF5 bash
  mkdir -p ${SOURCE_DIR}/hdf5-${HDF5_VERSION}
  cd ${SOURCE_DIR}/hdf5-${HDF5_VERSION}
  curl -L ${HDF5_URL} | tar --strip-components=2 -xz
  ./configure --enable-fortran --prefix=${HDF5_DIR}
  make all install
  cd ${SOURCE_DIR} && rm -rf hdf5-${HDF5_VERSION}
INSTALL_HDF5
ENV PATH="${PATH}:${HDF5_DIR}/bin"
ENV LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${HDF5_DIR}/lib"

# install netcdf-c
ENV NETCDF_VERSION="4.7.4"
ENV NETCDF_URL="https://github.com/Unidata/netcdf-c/archive/refs/tags/v${NETCDF_VERSION}.tar.gz"
ENV NETCDF_DIR="${STACK_DIR}/netcdf-${NETCDF_VERSION}"
RUN <<INSTALL_NETCDF_C bash
  mkdir -p ${SOURCE_DIR}/netcdf-c-${NETCDF_VERSION}
  cd ${SOURCE_DIR}/netcdf-c-${NETCDF_VERSION}
  curl -L ${NETCDF_URL} | tar --strip-components=1 -xz
  CPPFLAGS=-I${HDF5_DIR}/include LDFLAGS=-L${HDF5_DIR}/lib \
  ./configure --enable-netcdf-4 --disable-dap --prefix=${NETCDF_DIR}
  make all install
  cd ${SOURCE_DIR} && rm -rf netcdf-c-${NETCDF_VERSION}
INSTALL_NETCDF_C
ENV PATH="${PATH}:${NETCDF_DIR}/bin"
ENV LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${NETCDF_DIR}/lib"

# install netcdf-fortran into NETCDF_DIR
ENV NETCDFF_VERSION="4.4.5"
ENV NETCDFF_URL="https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v${NETCDFF_VERSION}.tar.gz"
RUN <<INSTALL_NETCDF_F bash
  mkdir -p ${SOURCE_DIR}/netcdf-fortran-${NETCDFF_VERSION}
  cd ${SOURCE_DIR}/netcdf-fortran-${NETCDFF_VERSION}
  curl -L ${NETCDFF_URL} | tar --strip-components=1 -xz
  CPPFLAGS=-I${NETCDF_DIR}/include LDFLAGS=-L${NETCDF_DIR}/lib \
  ./configure --prefix=${NETCDF_DIR}
  make all install
  cd ${SOURCE_DIR} && rm -rf netcdf-fortran-${NETCDFF_VERSION}
INSTALL_NETCDF_F

