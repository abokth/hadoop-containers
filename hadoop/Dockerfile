FROM quay.io/abokth/hadoop-containers-hadoopmvn AS hadoopmvn
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"


FROM quay.io/abokth/hadoop-containers-protobuf AS protobuf
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"


FROM quay.io/abokth/hadoop-containers-isa-l AS isa-l
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"


FROM hadoopmvn AS hadoopbuild

# Dist sources along with binaries.
COPY --from=protobuf /src/protobuf /src/protobuf
COPY --from=protobuf /app/protobuf /app/protobuf

# Dist sources along with binaries.
COPY --from=isa-l /src/isa-l /src/isa-l
COPY --from=isa-l /app/isa-l /app/isa-l

RUN env PROTOBUF_HOME=/app/protobuf PKG_CONFIG_PATH=/app/protobuf/lib/pkgconfig mvn package -Pdist,native -DskipTests -Dmaven.javadoc.skip -Disal.prefix=/app/isa-l || sleep 60
RUN env PROTOBUF_HOME=/app/protobuf PKG_CONFIG_PATH=/app/protobuf/lib/pkgconfig mvn package -Pdist,native -DskipTests -Dmaven.javadoc.skip -Disal.prefix=/app/isa-l || sleep 360
RUN env PROTOBUF_HOME=/app/protobuf PKG_CONFIG_PATH=/app/protobuf/lib/pkgconfig mvn package -Pdist,native -DskipTests -Dmaven.javadoc.skip -Disal.prefix=/app/isa-l
RUN mv hadoop-dist/target/hadoop-3.3.0.tar.gz /app/
WORKDIR /home/build/
RUN rm -rf /home/build/hadoop-3.3.0-src


FROM quay.io/abokth/hadoop-containers-runtime

COPY --from=hadoopbuild /src /src
COPY --from=hadoopbuild /app /app
