# Match alpine:latest (https://hub.docker.com/_/alpine/).
FROM alpine:3.9

# Install su-exec (https://github.com/ncopa/su-exec/commit/dddd1567b7c76365e1e0aac561287975020a8fad).
# Install shadow, which has groupadd and useradd.
RUN apk update \
 && apk add --no-cache \
      shadow \
      su-exec

# Install tini for run-non-root's --init option.
ADD https://github.com/krallin/tini/releases/download/v0.18.0/tini-static /usr/local/bin/tini
RUN chmod +rx /usr/local/bin/tini

# Install run-non-root.
ADD https://raw.githubusercontent.com/creemama/run-non-root/v1.5.1/run-non-root.sh /usr/local/bin/run-non-root
RUN chmod +rx /usr/local/bin/run-non-root

# Warn users if they are running as root.
# See the "Invoked with name sh" section of
# https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html .
# See also https://www.tldp.org/LDP/Linux-Filesystem-Hierarchy/html/etc.html .
ENV ENV /etc/profile
RUN printf "%s" "if [ "\$\(whoami\)" = 'root' ]; then printf \"\n\033[1;31m%s\033[0m\n\n\" \"WARNING: YOU ARE RUNNING AS THE ROOT USER!\"; fi" \
 >> /etc/profile.d/warn-root.sh

ENTRYPOINT ["run-non-root"]
