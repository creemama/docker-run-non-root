# Match alpine:latest (https://hub.docker.com/_/alpine/).
FROM alpine:3.8

# Install su-exec (https://github.com/ncopa/su-exec/commit/dddd1567b7c76365e1e0aac561287975020a8fad).
# Install shadow, which has groupadd and useradd.
RUN apk update \
 && apk add --no-cache \
      shadow \
      su-exec

# Install tini for run-non-root's --init option.
ADD https://github.com/krallin/tini/releases/download/v0.18.0/tini-static /usr/local/bin/tini
RUN chmod +x /usr/local/bin/tini

# Install run-non-root.
ADD https://raw.githubusercontent.com/creemama/run-non-root/v0.0.0/run-non-root.sh /usr/local/bin/run-non-root
RUN chmod +x /usr/local/bin/run-non-root

ENTRYPOINT ["run-non-root"]
