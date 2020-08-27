FROM quay.io/abokth/hadoop-containers-buildenv AS buildenv
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"


FROM buildenv

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
