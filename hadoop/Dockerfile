# Copyright 2020 Kungliga Tekniska högskolan

# Permission is hereby granted, free of charge, to any person
# obtaining a copy of this software and associated documentation files
# (the "Software"), to deal in the Software without restriction,
# including without limitation the rights to use, copy, modify, merge,
# publish, distribute, sublicense, and/or sell copies of the Software,
# and to permit persons to whom the Software is furnished to do so,
# subject to the following conditions:

# The above copyright notice and this permission notice shall be
# included in all copies or substantial portions of the Software.

# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
# EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
# MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
# NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS
# BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN
# ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
# CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

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
