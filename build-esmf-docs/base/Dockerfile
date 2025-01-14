#############################################
# Documentation build dependencies for ESMF #
#############################################

FROM ubuntu:plucky

# install packages
RUN <<INSTALL_PACKAGES bash
  apt-get update
  ln -fs /usr/share/zoneinfo/America/Denver /etc/localtime
  apt-get install -y tzdata
  dpkg-reconfigure --frontend noninteractive tzdata
  apt-get install -y \
    texlive-full \
    latex2html \
    perl \
    csh \
    git \
    build-essential \
    zip \
    gfortran
  apt-get clean
INSTALL_PACKAGES
