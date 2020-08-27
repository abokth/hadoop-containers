FROM centos:8

# Install runtime deps only.
RUN dnf -y update
RUN dnf -y install keyutils-libs krb5-libs libcom_err libgcc libselinux libstdc++ libtirpc openssl-libs pcre2 snappy zlib
RUN dnf clean all

