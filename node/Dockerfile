# https://nodejs.org/en/download/current/
# https://hub.docker.com/_/node/
FROM node:10.5.0-alpine

##########
# Start a copy of run-non-root's Alpine Dockerfile.
# https://github.com/creemama/docker-run-non-root/blob/1.4.0/alpine/Dockerfile

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
ADD https://raw.githubusercontent.com/creemama/run-non-root/v1.4.0/run-non-root.sh /usr/local/bin/run-non-root
RUN chmod +x /usr/local/bin/run-non-root

# End the copy of run-non-root's Alpine Dockerfile.
##########

# Remove the node user defined in
# https://github.com/nodejs/docker-node/blob/72dd945d29dee5afa73956ebc971bf3a472442f7/6/jessie/Dockerfile .
# Package shadow has userdel.
RUN userdel -r node

ENV RUN_NON_ROOT_COMMAND node
ENV RUN_NON_ROOT_USER node

ENTRYPOINT ["run-non-root"]
CMD ["--init"]
