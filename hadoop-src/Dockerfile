FROM centos:8 AS fetchverify

RUN mkdir /src
ADD http://apache.mirrors.spacedump.net/hadoop/common/hadoop-3.3.0/hadoop-3.3.0-src.tar.gz /src/
RUN sha512sum /src/hadoop-3.3.0-src.tar.gz | fgrep 3363782625e28402947c6a0bd5dddcc3481c8691ba59cbb369634bc5b1ae610e1eed9b542ce151c86ec8faa69df90fb53f35c2e4133058075e53728a22800f3d

FROM scratch
COPY --from=fetchverify /src/hadoop-3.3.0-src.tar.gz /
