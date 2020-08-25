FROM hadoop-src as hadoopsrc
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"

FROM centos:8

RUN useradd build
USER build
WORKDIR /home/build

# * Install development tools such as GCC, autotools, OpenJDK and Maven.
USER root
RUN dnf -y group install --with-optional 'Development Tools'
USER root
RUN dnf -y install java-1.8.0-openjdk-devel maven

# * Install Protocol Buffers v3.7.1.
USER build
RUN git clone https://github.com/protocolbuffers/protobuf
USER build
WORKDIR /home/build/protobuf
USER build
RUN git checkout v3.7.1
USER build
RUN autoreconf -i
USER build
RUN ./configure --prefix=/usr/local
USER build
RUN make -j3
USER root
RUN make install
USER build
WORKDIR /home/build

# * Install libraries provided by CentOS 8.
USER root
RUN dnf -y install libtirpc-devel zlib-devel lz4-devel bzip2-devel openssl-devel cyrus-sasl-devel libpmem-devel

# * Install optional dependencies (snappy-devel).
USER root
RUN dnf -y --enablerepo=PowerTools install snappy-devel

# * Install optional dependencies (libzstd-devel).
USER root
RUN dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
USER root
RUN dnf -y --enablerepo=epel install libzstd-devel

# * Install optional dependencies (isa-l).
USER root
RUN dnf -y --enablerepo=PowerTools install nasm
USER build
RUN git clone https://github.com/intel/isa-l
USER build
WORKDIR /home/build/isa-l
USER build
RUN ./autogen.sh
USER build
RUN ./configure
USER build
RUN make -j3
USER root
RUN make install

USER build
WORKDIR /home/build
COPY --from=hadoopsrc /hadoop-3.3.0-src.tar.gz /home/build
USER root
RUN tar xf hadoop-3.3.0-src.tar.gz
RUN chown -R build:build hadoop-3.3.0-src
USER build
WORKDIR /home/build/hadoop-3.3.0-src
RUN mvn package -Pdist,native -DskipTests -Dmaven.javadoc.skip
