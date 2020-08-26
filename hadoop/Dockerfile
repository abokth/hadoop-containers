FROM quay.io/abokth/hadoop-containers-hadoop-src as hadoopsrc
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"


FROM centos:8 AS runtime

# Install runtime deps only.
RUN dnf -y update
RUN dnf -y install keyutils-libs krb5-libs libcom_err libgcc libselinux libstdc++ libtirpc openssl-libs pcre2 snappy zlib
RUN dnf clean all

FROM runtime AS buildenv

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


FROM buildenv AS protobuf

# * Install Protocol Buffers v3.7.1.
RUN git clone -b v3.7.1 https://github.com/protocolbuffers/protobuf
WORKDIR protobuf
RUN autoreconf -i
RUN mkdir /home/build/protobuf-build
WORKDIR /home/build/protobuf-build
RUN /src/protobuf/configure --prefix=/app/protobuf
RUN make -j3
RUN make install
WORKDIR /home/build
RUN rm -rf /home/build/protobuf-build


FROM buildenv AS isal

RUN git clone https://github.com/intel/isa-l
WORKDIR isa-l
RUN ./autogen.sh
RUN mkdir /home/build/isa-l-build
WORKDIR /home/build/isa-l-build
RUN /src/isa-l/configure --prefix=/app/isa-l
RUN make -j3
RUN make install
WORKDIR /home/build
RUN rm -rf /home/build/isa-l-build


FROM buildenv AS hadoopbuild

COPY --from=hadoopsrc /hadoop-3.3.0-src.tar.gz /src/
USER root
WORKDIR /home/build
RUN tar xf /src/hadoop-3.3.0-src.tar.gz
RUN chown -R build:build hadoop-3.3.0-src
USER build
WORKDIR /home/build/hadoop-3.3.0-src

# Dist sources along with binaries.
COPY --from=protobuf /src/protobuf /src/protobuf
COPY --from=protobuf /app/protobuf /app/protobuf

# Dist sources along with binaries.
COPY --from=isal /src/isa-l /src/isa-l
COPY --from=isal /app/isa-l /app/isa-l

RUN env PROTOBUF_HOME=/app/protobuf PKG_CONFIG_PATH=/app/protobuf/lib/pkgconfig mvn package -Pdist,native -DskipTests -Dmaven.javadoc.skip -Disal.prefix=/app/isa-l
RUN mv hadoop-dist/target/hadoop-3.3.0.tar.gz /app/
WORKDIR /home/build/
RUN rm -rf /home/build/hadoop-3.3.0-src


FROM runtime

COPY --from=hadoopbuild /src /src
COPY --from=hadoopbuild /app /app
