FROM quay.io/abokth/hadoop-containers-hadoop-src as hadoopsrc
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"


FROM quay.io/abokth/hadoop-containers-runtime AS runtime
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"


FROM quay.io/abokth/hadoop-containers-buildenv AS buildenv
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"


FROM quay.io/abokth/hadoop-containers-hadoopmvn AS hadoopmvn


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


FROM hadoopmvn AS hadoopbuild

# Dist sources along with binaries.
COPY --from=protobuf /src/protobuf /src/protobuf
COPY --from=protobuf /app/protobuf /app/protobuf

# Dist sources along with binaries.
COPY --from=isal /src/isa-l /src/isa-l
COPY --from=isal /app/isa-l /app/isa-l

RUN env PROTOBUF_HOME=/app/protobuf PKG_CONFIG_PATH=/app/protobuf/lib/pkgconfig mvn package -Pdist,native -DskipTests -Dmaven.javadoc.skip -Disal.prefix=/app/isa-l || sleep 60
RUN env PROTOBUF_HOME=/app/protobuf PKG_CONFIG_PATH=/app/protobuf/lib/pkgconfig mvn package -Pdist,native -DskipTests -Dmaven.javadoc.skip -Disal.prefix=/app/isa-l || sleep 360
RUN env PROTOBUF_HOME=/app/protobuf PKG_CONFIG_PATH=/app/protobuf/lib/pkgconfig mvn package -Pdist,native -DskipTests -Dmaven.javadoc.skip -Disal.prefix=/app/isa-l
RUN mv hadoop-dist/target/hadoop-3.3.0.tar.gz /app/
WORKDIR /home/build/
RUN rm -rf /home/build/hadoop-3.3.0-src


FROM runtime

COPY --from=hadoopbuild /src /src
COPY --from=hadoopbuild /app /app
