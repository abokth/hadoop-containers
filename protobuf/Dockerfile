FROM quay.io/abokth/hadoop-containers-buildenv

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
