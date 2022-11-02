# syntax=docker/dockerfile:1

# base image
FROM ubuntu:20.04

# non interactive mode
ENV DEBIAN_FRONTEND noninteractive

# environment variables passed to image
ENV HOSTTYPE=x86_64
ENV OSNAME=ubuntu
ENV OS_VERSION=20.04
ENV OSTYPE=linux
ENV COMPILER=gnu
ENV COMPILER_VERSION=9

# labels
LABEL description="Image for ESMF Build Environment"
LABEL version="1.0"
LABEL "org.earthsystemmodeling.hosttype"=${HOSTTYPE}
LABEL "org.earthsystemmodeling.ostype"=${OSTYPE}
LABEL "org.earthsystemmodeling.osname"=${OSNAME}
LABEL "org.earthsystemmodeling.osname.version"=${OS_VERSION}
LABEL "org.earthsystemmodeling.compiler"=${COMPILER}
LABEL "org.earthsystemmodeling.compiler.vers"=${COMPILER_VERSION}

# root setup
RUN <<ROOT_SETUP bash
  echo 'root:password' | chpasswd
  ln -sf /bin/bash /bin/sh
  ln -sf /usr/bin/make /usr/bin/gmake
ROOT_SETUP

# install packages
RUN <<INSTALL_PACKAGES bash
  apt-get update
  ln -fs /usr/share/zoneinfo/America/New_York /etc/localtime
  apt-get install -y tzdata
  dpkg-reconfigure --frontend noninteractive tzdata
  apt-get install -y \
    build-essential \
    gcc-9 \
    gfortran-9 \
    wget \
    curl \
    m4 \
    zlib1g \
    zlib1g-dev \
    libssl-dev \
    python3 \
    python3-pip \
    git \
    vim \
    csh
  apt-get clean
  pip3 install PyYAML
INSTALL_PACKAGES

# set compilers
ENV CC  gcc-9
ENV CXX g++-9
ENV F77 gfortran-9 
ENV F90 gfortran-9
ENV FC  gfortran-9

# create users
ENV DEVHOME=/home/dev
ENV DEVDNL=${DEVHOME}/downloads
ENV DEVSRC=${DEVHOME}/source
ENV DEVSTK=${DEVHOME}/stack
RUN <<ADD_USER_DEV bash
  groupadd -r dev
  useradd -r -m -d ${DEVHOME} -s /bin/bash -g dev dev
  echo 'dev:password' | chpasswd
  mkdir -p ${DEVDNL}
  mkdir -p ${DEVSRC}
  mkdir -p ${DEVSTK}
  chown -R dev:dev ${DEVHOME}
ADD_USER_DEV

USER dev

# install cmake
ENV CMAKE_VERSION=3.18.2
ENV CMAKE_URL=https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}.tar.gz
ENV CMAKE_DIR=${DEVSTK}/cmake-${CMAKE_VERSION}
RUN <<INSTALL_CMAKE bash
  mkdir -p ${DEVSRC}/cmake-${CMAKE_VERSION}
  cd ${DEVSRC}/cmake-${CMAKE_VERSION}
  wget ${CMAKE_URL} -O cmake-${CMAKE_VERSION}.tar.gz
  tar --strip-components=1 -xzf cmake-${CMAKE_VERSION}.tar.gz
  ./bootstrap --prefix=${CMAKE_DIR}
  make all install
  cd ${DEVSRC} && rm -rf cmake-${CMAKE_VERSION}
INSTALL_CMAKE
ENV PATH="${PATH}:${CMAKE_DIR}/bin"
ENV CMAKE ${CMAKE_DIR}/bin/cmake
ENV CTEST ${CMAKE_DIR}/bin/ctest

# install openmpi
ENV OPENMPI_VERSION=3.0.0
ENV OPENMPI_URL=https://download.open-mpi.org/release/open-mpi/v3.0/openmpi-${OPENMPI_VERSION}.tar.gz
ENV OPENMPI_DIR=${DEVSTK}/openmpi-${OPENMPI_VERSION}
RUN <<INSTALL_OPENMPI bash
  mkdir -p ${DEVSRC}/openmpi-${OPENMPI_VERSION}
  cd ${DEVSRC}/openmpi-${OPENMPI_VERSION}
  wget ${OPENMPI_URL} -O openmpi-${OPENMPI_VERSION}.tar.gz
  tar --strip-components=1 -zxf openmpi-${OPENMPI_VERSION}.tar.gz
  ./configure --enable-fortran --prefix=${OPENMPI_DIR}
  make all install
  cd ${DEVSRC} && rm -rf openmpi-${OPENMPI_VERSION}
INSTALL_OPENMPI
ENV PATH="${PATH}:${OPENMPI_DIR}/bin"
ENV LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${OPENMPI_DIR}/lib"

# set compilers
ENV CC  mpicc
ENV CXX mpicxx
ENV F77 mpif77
ENV F90 mpif90
ENV FC  mpifort

# install hdf5
ENV HDF5_VERSION=1.12.2
ENV HDF5_URL=https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.12/hdf5-1.12.2/src/hdf5-${HDF5_VERSION}.tar.gz
ENV HDF5_DIR=${DEVSTK}/hdf5-${HDF5_VERSION}
RUN <<INSTALL_HDF5 bash
  mkdir -p ${DEVSRC}/hdf5-${HDF5_VERSION}
  cd ${DEVSRC}/hdf5-${HDF5_VERSION}
  wget ${HDF5_URL} -O hdf5-${HDF5_VERSION}.tar.gz
  tar --strip-components=2 -xzf hdf5-${HDF5_VERSION}.tar.gz
  ./configure --enable-fortran --prefix=${HDF5_DIR}
  make all install
  cd ${DEVSRC} && rm -rf hdf5-${HDF5_VERSION}
INSTALL_HDF5
ENV PATH="${PATH}:${HDF5_DIR}/bin"
ENV LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${HDF5_DIR}/lib"

# install netcdf-c
ENV NETCDF_VERSION=4.7.4
ENV NETCDF_URL=https://github.com/Unidata/netcdf-c/archive/refs/tags/v${NETCDF_VERSION}.tar.gz
ENV NETCDF_DIR=${DEVSTK}/netcdf-${NETCDF_VERSION}
RUN <<INSTALL_NETCDF_C bash
  mkdir -p ${DEVSRC}/netcdf-c-${NETCDF_VERSION}
  cd ${DEVSRC}/netcdf-c-${NETCDF_VERSION}
  wget ${NETCDF_URL} -O netcdfc-${NETCDF_VERSION}.tar.gz
  tar --strip-components=1 -xzf netcdfc-${NETCDF_VERSION}.tar.gz
  CPPFLAGS=-I${HDF5_DIR}/include LDFLAGS=-L${HDF5_DIR}/lib \
  ./configure --enable-netcdf-4 --disable-dap --prefix=${NETCDF_DIR}
  make all install
  cd ${DEVSRC} && rm -rf netcdf-c-${NETCDF_VERSION}
INSTALL_NETCDF_C
ENV PATH="${PATH}:${NETCDF_DIR}/bin"
ENV LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${NETCDF_DIR}/lib"

# install netcdf-fortran into NETCDF_DIR
ENV NETCDFF_VERSION=4.4.5
ENV NETCDFF_URL=https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v${NETCDFF_VERSION}.tar.gz
RUN <<INSTALL_NETCDF_F bash
  mkdir -p ${DEVSRC}/netcdf-fortran-${NETCDFF_VERSION}
  cd ${DEVSRC}/netcdf-fortran-${NETCDFF_VERSION}
  wget ${NETCDFF_URL} -O netcdff-${NETCDFF_VERSION}.tar.gz
  tar --strip-components=1 -xzf netcdff-${NETCDFF_VERSION}.tar.gz
  CPPFLAGS=-I${NETCDF_DIR}/include LDFLAGS=-L${NETCDF_DIR}/lib \
  ./configure --prefix=${NETCDF_DIR}
  make all install
  cd ${DEVSRC} && rm -rf netcdf-fortran-${NETCDFF_VERSION}
INSTALL_NETCDF_F

# esmf build environment
ENV ESMF_ABI="64"
ENV ESMF_BOPT="O"
ENV ESMF_COMM="openmpi"
ENV ESMF_COMPILER="gfortran"
ENV ESMF_OS="Linux"
ENV ESMF_SITE="default"
ENV ESMF_NETCDF="split"
ENV ESMF_NETCDF_LIBPATH="${NETCDF_DIR}/lib"
ENV ESMF_NETCDF_INCLUDE="${NETCDF_DIR}/include"
ENV ESMF_ARRAY_LITE="FALSE"
ENV ESMF_NO_INTEGER_1_BYTE="TRUE"
ENV ESMF_NO_INTEGER_2_BYTE="TRUE"
ENV ESMF_FORTRANSYMBOLS="default"
ENV ESMF_MAPPER_BUILD="OFF"
ENV ESMF_AUTO_LIB_BUILD="ON"
ENV ESMF_DEFER_LIB_BUILD="ON"
ENV ESMF_SHARED_LIB_BUILD="ON"
ENV ESMF_INSTALL_MODDIR="mod"
ENV ESMF_INSTALL_BINDIR="bin"
ENV ESMF_INSTALL_LIBDIR="lib"
ENV ESMF_INSTALL_PREFIX="${DEVSTK}/esmf"

# default execution command
WORKDIR ${DEVHOME}
SHELL ["/bin/bash","-ec"]
CMD ["/bin/bash","-l"]

