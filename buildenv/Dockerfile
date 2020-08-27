FROM quay.io/abokth/hadoop-containers-runtime AS runtime

FROM runtime

# * Install development tools such as GCC, autotools, OpenJDK and Maven.
RUN dnf -y group install --with-optional 'Development Tools'
RUN dnf -y install java-1.8.0-openjdk-devel maven

# * Install libraries provided by CentOS 8.
RUN dnf -y install libtirpc-devel zlib-devel lz4-devel bzip2-devel openssl-devel cyrus-sasl-devel libpmem-devel

# * Install optional dependencies (snappy-devel).
RUN dnf -y --enablerepo=PowerTools install snappy-devel

# * Install optional dependencies (libzstd-devel).
RUN dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
RUN dnf -y --enablerepo=epel install libzstd-devel

# * Install optional dependencies (isa-l).
RUN dnf -y --enablerepo=PowerTools install nasm

RUN useradd build
RUN mkdir /app /src
RUN chown build:build /app /src
WORKDIR /src
USER build
