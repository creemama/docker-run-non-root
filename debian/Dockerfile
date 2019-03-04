# Match debian:latest (https://hub.docker.com/_/debian/).
FROM debian:9.7

# This file closely mimics the Ubuntu one. If you make updates here, make
# updates there as well.

# Learn from httpd and PostgreSQL. They use Debian in their Dockerfiles.
# https://github.com/docker-library/httpd/blob/38842a5d4cdd44ff4888e8540c0da99009790d01/2.4/Dockerfile
# https://github.com/docker-library/postgres/blob/69bc540ecfffecce72d49fa7e4a46680350037f9/9.6/Dockerfile

# "Best practices for writing Dockerfiles" > "RUN" > "APT-GET"
# https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#apt-get

# Install su-exec (https://github.com/ncopa/su-exec/commit/dddd1567b7c76365e1e0aac561287975020a8fad).
ADD https://github.com/ncopa/su-exec/archive/dddd1567b7c76365e1e0aac561287975020a8fad.zip su-exec.zip
RUN apt-get update \
 && apt-get install --no-install-recommends -y \
      gcc \
      libc-dev \
      make \
      unzip \
 && rm -rf /var/lib/apt/lists/* \
 && unzip su-exec.zip \
 && cd su-exec-dddd1567b7c76365e1e0aac561287975020a8fad \
 && make \
 && mv su-exec /usr/local/bin \
 && cd .. \
 && rm -rf su-exec.zip su-exec-dddd1567b7c76365e1e0aac561287975020a8fad \
 && apt-get purge --auto-remove -y \
      gcc \
      libc-dev \
      make \
      unzip \
 && rm -rf /var/lib/apt/lists/*

# Install tini for run-non-root's --init option.
ADD https://github.com/krallin/tini/releases/download/v0.18.0/tini-static /usr/local/bin/tini
RUN chmod +rx /usr/local/bin/tini

# Install run-non-root.
ADD https://raw.githubusercontent.com/creemama/run-non-root/v1.4.0/run-non-root.sh /usr/local/bin/run-non-root
RUN chmod +rx /usr/local/bin/run-non-root

# Warn users if they are running as root.
# See the "Invoked with name sh" section of
# https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html .
# See also https://www.tldp.org/LDP/Linux-Filesystem-Hierarchy/html/etc.html .
ENV ENV /etc/profile
RUN printf "%s" "if [ "\$\(whoami\)" = 'root' ]; then printf \"\n\033[1;31m%s\033[0m\n\n\" \"WARNING: YOU ARE RUNNING AS THE ROOT USER!\"; fi" \
 >> /etc/profile.d/warn-root.sh

# Install ps for testing.
RUN apt-get update \
 && apt-get install --no-install-recommends -y procps \
 && rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["run-non-root"]
