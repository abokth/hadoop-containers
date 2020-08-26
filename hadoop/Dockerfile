FROM hadoop-src as hadoopsrc
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"


FROM centos:8 AS protobuf

# * Install development tools such as GCC, autotools, OpenJDK and Maven.
RUN dnf -y group install --with-optional 'Development Tools'
RUN dnf -y install java-1.8.0-openjdk-devel maven

# * Install Protocol Buffers v3.7.1.
RUN useradd build
USER build
WORKDIR /home/build
RUN git clone -b v3.7.1 https://github.com/protocolbuffers/protobuf
WORKDIR /home/build/protobuf
RUN autoreconf -i
RUN mkdir ../protobuf-build
WORKDIR /home/build/protobuf-build
RUN ../protobuf/configure --prefix=/home/build/protobuf-install
RUN make -j3
RUN make install
WORKDIR /home/build
RUN rm -rf /home/build/protobuf-build


FROM centos:8 AS isal

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
USER build
WORKDIR /home/build
RUN git clone https://github.com/intel/isa-l
WORKDIR /home/build/isa-l
RUN ./autogen.sh
RUN mkdir ../isa-l-build
WORKDIR /home/build/isa-l-build
RUN ../isa-l/configure --prefix=/home/build/isa-l-install
RUN make -j3
RUN make install
WORKDIR /home/build
RUN rm -rf /home/build/isa-l-build


FROM centos:8

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
WORKDIR /home/build

COPY --from=hadoopsrc /hadoop-3.3.0-src.tar.gz /home/build
USER root
RUN tar xf hadoop-3.3.0-src.tar.gz
RUN chown -R build:build hadoop-3.3.0-src
USER build
WORKDIR /home/build/hadoop-3.3.0-src

# Dist sources along with binaries.
COPY --from=protobuf /home/build/protobuf /home/build/protobuf
COPY --from=protobuf /home/build/protobuf-install /home/build/protobuf-install

# Dist sources along with binaries.
COPY --from=isal /home/build/isa-l /home/build/isa-l
COPY --from=isal /home/build/isa-l-install /home/build/isa-l-install

RUN env PROTOBUF_HOME=/home/build/protobuf-install PKG_CONFIG_PATH=/home/build/protobuf-install/lib/pkgconfig mvn package -Pdist,native -DskipTests -Dmaven.javadoc.skip -Disal.prefix=/home/build/isa-l-install
