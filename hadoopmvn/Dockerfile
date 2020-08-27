FROM quay.io/abokth/hadoop-containers-hadoop-src as hadoopsrc
ENV FOO="Workaround https://github.com/containers/buildah/issues/1920"


FROM quay.io/abokth/hadoop-containers-buildenv

COPY --from=hadoopsrc /hadoop-3.3.0-src.tar.gz /src/
USER root
WORKDIR /home/build
RUN tar xf /src/hadoop-3.3.0-src.tar.gz
RUN chown -R build:build hadoop-3.3.0-src
USER build
WORKDIR /home/build/hadoop-3.3.0-src

RUN mvn dependency:go-offline || sleep 180
RUN mvn dependency:go-offline || sleep 360
RUN mvn dependency:go-offline || sleep 720
