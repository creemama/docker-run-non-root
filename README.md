# run-non-root

> Run Docker containers with a non-root user by default.

[![Travis CI Build Status](https://img.shields.io/travis/creemama/run-non-root/master.svg?style=flat-square&label=Travis+CI)](https://travis-ci.org/creemama/run-non-root) [![run-non-root Version](https://img.shields.io/github/tag/creemama/run-non-root.svg?style=flat-square)](https://github.com/creemama/docker-run-non-root) [![run-non-root on Docker Hub](https://img.shields.io/docker/automated/jrottenberg/ffmpeg.svg?style=flat-square)](https://hub.docker.com/r/creemama/run-non-root/)

`run-non-root` runs Linux commands as a non-root user, creating a non-root user if necessary.

This allows us to

**run Docker containers with a non-root user by default**

without having to specify a `USER` with hardcoded UIDs and GIDs in our Dockerfiles.

# Supported tags and respective `Dockerfile` links

 * [`1.4.0-alpine`, `1.4-alpine`, `1-alpine`, `1.4.0`, `1.4`, `1`, `latest` *(alpine/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/1.4.0/alpine/Dockerfile)
 * [`1.4.0-centos`, `1.4-centos`, `1-centos`, `centos` *(centos/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/1.4.0/centos/Dockerfile)
 * [`1.4.0-debian`, `1.4-debian`, `1-debian`, `debian` *(debian/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/1.4.0/debian/Dockerfile)
 * [`1.4.0-fedora`, `1.4-fedora`, `1-fedora`, `fedora` *(fedora/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/1.4.0/fedora/Dockerfile)
 * [`1.4.0-ubuntu`, `1.4-ubuntu`, `1-ubuntu`, `ubuntu` *(ubuntu/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/1.4.0/ubuntu/Dockerfile)

**Examples**

 * [`1.4.0-certbot` *(certbot/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/1.4.0/certbot/Dockerfile)
 * [`1.4.0-certbot-renew-cron` *(certbot-renew-cron/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/1.4.0/certbot-renew-cron/Dockerfile)
 * [`1.4.0-node` *(node/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/1.4.0/node/Dockerfile)

# run-non-root

[run-non-root](https://github.com/creemama/run-non-root) is a shell script that runs Linux commands as a non-root user.

```
Usage:
  run-non-root [options] [--] [COMMAND] [ARGS...]

Run Linux commands as a non-root user, creating a non-root user if necessary.

Options:
  -c, --chown             Colon-separated list of files and directories to run
                          "chown USERNAME:GID" on before executing the
                          command; you can use this option multiple times
                          instead of using a colon-separated list; run-non-root
                          ignores this option if you are already running as a
                          non-root user; unlike -p this option is non-recursive.
  -d, --debug             Output debug information; using --quiet does not
                          silence debug output. Double up (-dd) for more output.
  -f, --group GROUP_NAME  The group name to use when executing the command; the
                          default group name is USERNAME or nonroot; this
                          option is ignored if we are already running as a
                          non-root user or if the GID already exists; this
                          option overrides the RUN_NON_ROOT_GROUP environment
                          variable.
  -g, --gid GID           The group ID to use when executing the command; the
                          default GID is UID or a new ID determined by
                          groupadd; this option is ignored if we are already
                          running as a non-root user; this option overrides the
                          RUN_NON_ROOT_GID environment variable.
  -h, --help              Output this help message and exit.
  -i, --init              Run an init (the tini command) that forwards signals
                          and reaps processes; this matches the docker run
                          option --init.
  -p, --path              Colon-separated list of directories to run
                          "chown -R USERNAME:GID" on before executing the
                          command; you can use this option multiple times
                          instead of using a colon-separated list; if a
                          directory does not exist, run-non-root attempts to
                          create it; run-non-root ignores this option if you
                          are already running as a non-root user; unlike -c
                          this option is recursive.
  -q, --quiet             Do not output "Running ( COMMAND ) as USER_INFO ..."
                          or warnings; this option does not silence --debug
                          output.
  -t, --user USERNAME     The username to use when executing the command; the
                          default is nonroot; this option is ignored if we are
                          already running as a non-root user or if the UID
                          already exists; this option overrides the
                          RUN_NON_ROOT_USER environment variable.
  -u, --uid UID           The user ID to use when executing the command; the
                          default UID is GID or a new ID determined by
                          useraddd; this option is ignored if we are already
                          running as a non-root user; this option overrides the
                          RUN_NON_ROOT_UID environment variable.
  -v, --version           Ouput the version number of run-non-root.

Environment Variables:
  RUN_NON_ROOT_COMMAND    The command to execute if a command is not given; the
                          default is bash; if bash does not exist, the default
                          is sh.
  RUN_NON_ROOT_GID        The group ID to use when executing the command; see
                          the --gid option for more info.
  RUN_NON_ROOT_GROUP      The group name to use when executing the command; see
                          the --group option for more info.
  RUN_NON_ROOT_UID        The user ID to use when executing the command; see
                          the --uid option for more info.
  RUN_NON_ROOT_USER       The username to use when executing the command; see
                          the --user option for more info.

Examples:
  # Run bash or sh as a non-root user.
  run-non-root

  # Run id as a non-root user.
  run-non-root -- id

  # Run id as a non-root user using options and the given user specification.
  run-non-root -f ec2-user -g 1000 -t ec2-user -u 1000 -- id

  # Run id as a non-root user using environment variables
  # and the given user specification.
  export RUN_NON_ROOT_GID=1000
  export RUN_NON_ROOT_GROUP=ec2-user
  export RUN_NON_ROOT_UID=1000
  export RUN_NON_ROOT_USER=ec2-user
  run-non-root -- id
```

# Examples

```sh
# Run sh as a non-root user.
docker run -it --rm creemama/run-non-root:latest
# Output: Running ( su-exec nonroot:1000 sh ) as uid=1000(nonroot) gid=1000(nonroot) groups=1000(nonroot) ...

# Run id as a non-root user.
docker run -it --rm creemama/run-non-root:latest --q -- id
# Output: uid=1000(nonroot) gid=1000(nonroot) groups=1000(nonroot)

# Run id as a non-root user using options and the given user specification.
docker run -it --rm creemama/run-non-root:latest \
  -f ec2-user -g 1000 -q -t ec2-user -u 1000 -- id
# Output: uid=1000(ec2-user) gid=1000(ec2-user) groups=1000(ec2-user)

# Run id as a non-root user using environment variables
# and the given user specification.
docker run \
  -e RUN_NON_ROOT_GID=1000 \
  -e RUN_NON_ROOT_GROUP=ec2-user \
  -e RUN_NON_ROOT_UID=1000 \
  -e RUN_NON_ROOT_USER=ec2-user \
  -it --rm creemama/run-non-root:latest \
  -q -- id
# Output: uid=1000(ec2-user) gid=1000(ec2-user) groups=1000(ec2-user)

# Run as yourself.
docker run -it --rm creemama/run-non-root:latest \
  -f $(id -gn) -g $(id -g) -t $(id -nu) -u $(id -u) \
  -- id

# Run as root if you need to.
docker run -it --rm creemama/run-non-root:latest -qu 0 -- whoami
# Output: root
```

# Docker and `run-non-root`

As we all know, [processes in containers should not run as root](https://medium.com/@mccode/processes-in-containers-should-not-run-as-root-2feae3f0df3b).

There are several approaches to run as a non-root user.

**Specify a `USER` in your Dockerfile**

One approach is to create a user via `useradd` and specify a [`USER`](https://docs.docker.com/engine/reference/builder/#user) in your Dockerfile.

```
FROM debian:stretch

RUN groupadd -g 999 appuser && \
    useradd -r -u 999 -g appuser appuser
USER appuser

CMD ["cat", "/tmp/secrets.txt"]
```

The upside to this approach is that the container, by default, runs as `appuser` instead of root.

The downside is that `appuser` has a specific UID and GID that [you cannot change without some work](https://www.cyberciti.biz/faq/linux-change-user-group-uid-gid-for-all-owned-files/).

**Specify a UID when starting your container**

Another approach is to use the `-u` or `--user` option.

```sh
docker run -it --rm \
  --user $(id -u):$(id -g) \               # Run as the given user.
  --volume $(app):/app \                   # Mount the source code.
  --workdir /app \                         # Set the working dir.
  my-docker/my-build-environment:latest \  # The build image
  make assets                              # The command
```

The upside to this approach is that you have control of the specific UID and GID of the user running the container.

The [downside](https://medium.com/redbubble/running-a-docker-container-as-a-non-root-user-7d2e00f8ee15) is that your user may be HOME-less and nameless. In other words,
the user might have no home directory and `whoami` might not find a name for the user.

Basically, unless the UID you specified is in the `getent passwd` list, your container does not know about the user you specified.

**Specify `run-non-root` as your `ENTRYPOINT`**

Using `run-non-root` as the `ENTRYPOINT` of your container overcomes the downsides of the aforementioned approaches.

From a base image:
```
FROM alpine:3.8

...

ADD https://raw.githubusercontent.com/creemama/run-non-root/master/run-non-root.sh /usr/local/bin/run-non-root
RUN chmod +x /usr/local/bin/run-non-root

ENTRYPOINT ["run-non-root"]
CMD ["--", "/your/program", "-and", "-its", "arguments"]
```

From one of run-non-root's images:
```
FROM creemama/run-non-root:1.4.0-alpine

...

CMD ["--", "/your/program", "-and", "-its", "arguments"]
```

With this approach, you do not have to specify `USER` in your Dockerfile or use the `--user` option when calling `docker run`. Your container runs as a non-root user by default.

If `run-non-root` creates the non-root user (which is nonroot by default), this user will have a home directory, and `whoami` will return that user's name.

# `tini`

Use `run-non-root` in conjunction with [`tini`](https://github.com/krallin/tini) to handle zombie reaping and signal forwarding by using the `--init` option.

```sh
$ docker run -it --rm creemama/run-non-root:latest --init -q -- ps aux
PID   USER     TIME  COMMAND
    1 nonroot   0:00 tini -- ps aux
   17 nonroot   0:00 ps aux
```
