# Match fedora:latest (https://hub.docker.com/_/fedora/).
FROM fedora:28

# This file closely mimics the CentOS one. If you make updates here, make
# updates there as well.

RUN yum -y update \
 && yum clean all

# Install su-exec (https://github.com/ncopa/su-exec/commit/dddd1567b7c76365e1e0aac561287975020a8fad).
ADD https://github.com/ncopa/su-exec/archive/dddd1567b7c76365e1e0aac561287975020a8fad.zip su-exec.zip
RUN yum install -y \
      gcc \
      make \
      unzip \
 && unzip su-exec.zip \
 && cd su-exec-dddd1567b7c76365e1e0aac561287975020a8fad \
 && make \
 && mv su-exec /usr/local/bin \
 && cd .. \
 && rm -rf su-exec.zip su-exec-dddd1567b7c76365e1e0aac561287975020a8fad \
 && yum remove -y \
      gcc \
      make \
      unzip

# Install tini for run-non-root's --init option.
ADD https://github.com/krallin/tini/releases/download/v0.18.0/tini-static /usr/local/bin/tini
RUN chmod +x /usr/local/bin/tini

# Install run-non-root.
ADD https://raw.githubusercontent.com/creemama/run-non-root/v0.0.0/run-non-root.sh /usr/local/bin/run-non-root
RUN chmod +x /usr/local/bin/run-non-root

# Install ps for testing.
RUN dnf install -y procps-ng

ENTRYPOINT ["run-non-root"]
