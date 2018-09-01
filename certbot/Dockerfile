# https://hub.docker.com/r/certbot/certbot/tags/
FROM certbot/certbot:v0.26.1

##########
# Start a copy of run-non-root's Alpine Dockerfile.
# https://github.com/creemama/docker-run-non-root/blob/1.3.0/alpine/Dockerfile

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
ADD https://raw.githubusercontent.com/creemama/run-non-root/v1.3.0/run-non-root.sh /usr/local/bin/run-non-root
RUN chmod +x /usr/local/bin/run-non-root

# End the copy of run-non-root's Alpine Dockerfile.
##########

RUN           cd /usr/local/bin \
           && printf "%s\n" "#!/bin/sh" \
  > script && printf "%s\n" \
 >> script && printf "%s\n" "exec run-non-root --path \"/etc/letsencrypt:/var/lib/letsencrypt:/var/log/letsencrypt\" \"\$@\"" \
 >> script && mv script run-non-root-certbot \
           && chmod +x run-non-root-certbot

ENV RUN_NON_ROOT_COMMAND "certbot"
ENV RUN_NON_ROOT_USER certbot

ENTRYPOINT ["run-non-root-certbot"]
