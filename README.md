# Supported tags and respective `Dockerfile` links

 * [`0.0.0`, `latest` *(alpine/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/0.0.0/alpine/Dockerfile)
 * [`0.0.0` *(centos/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/0.0.0/centos/Dockerfile)
 * [`0.0.0` *(debian/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/0.0.0/debian/Dockerfile)
 * [`0.0.0` *(fedora/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/0.0.0/fedora/Dockerfile)
 * [`0.0.0` *(ubuntu/Dockerfile)*](https://github.com/creemama/docker-run-non-root/blob/0.0.0/ubuntu/Dockerfile)

# run-non-root

[run-non-root](https://github.com/creemama/run-non-root) is a shell script that runs Linux commands as a non-root user, creating a non-root user if necessary.

```
Usage:
  run-non-root [options] [--] [COMMAND] [ARGS...]

Options:
  -d, --debug              Output debug information;
                           using --quiet does not silence debug output.
  -f, --gname GROUP_NAME   The group name to use when executing the command;
                           the default is nonrootgroup;
                           when specified, this option overrides the
                           RUN_NON_ROOT_GROUP_NAME environment variable.
  -g, --gid GROUP_ID       The group ID to use when executing the command;
                           the default is the first unused group ID
                           strictly less than 1000;
                           when specified, this option overrides the
                           RUN_NON_ROOT_GROUP_ID environment variable.
  -h, --help               Output this help message and exit.
  -q, --quiet              Do not output "Running COMMAND as USER_INFO ..."
                           or warnings; this option does not silence debug output.
  -t, --uname USER_NAME    The user name to use when executing the command;
                           the default is nonrootuser;
                           when specified, this option overrides the
                           RUN_NON_ROOT_USER_NAME environment variable.
  -u, --uid USER_ID        The user ID to use when executing the command;
                           the default is the first unused user ID
                           strictly less than 1000;
                           when specified, this option overrides the
                           RUN_NON_ROOT_USER_ID environment variable.

Environment Variables:
  RUN_NON_ROOT_COMMAND     The command to execute if a command is not given;
                           the default is sh.
  RUN_NON_ROOT_GROUP_ID    The group ID to use when executing the command;
                           the default is the first unused group ID
                           strictly less than 1000;
                           the -g or --gid options override this environment variable.
  RUN_NON_ROOT_GROUP_NAME  The user name to use when executing the command;
                           the default is nonrootgroup;
                           the -f or --gname options override this environment variable.
  RUN_NON_ROOT_USER_ID     The user ID to use when executing the command;
                           the default is the first unused user ID
                           strictly less than 1000;
                           the -u or --uid options override this environment variable.
  RUN_NON_ROOT_USER_NAME   The user name to use when executing the command;
                           the default is nonrootuser;
                           the -t or --uname options override this environment variable.
```

## Install `run-non-root`

Use the following commands to install or upgrade `run-non-root`:

```sh
wget -O /usr/local/bin/run-non-root https://raw.githubusercontent.com/creemama/run-non-root/master/run-non-root.sh
# curl -L https://raw.githubusercontent.com/creemama/run-non-root/master/run-non-root.sh -o /usr/local/bin/run-non-root
chmod +x /usr/local/bin/run-non-root
```

## Docker and `run-non-root`

As we all know, [processes in containers should not run as root](https://medium.com/@mccode/processes-in-containers-should-not-run-as-root-2feae3f0df3b).

There are several approaches to run as a non-root user.

### Specify a `USER` in your Dockerfile

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

### Specify a UID when starting your container

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

### Specify `run-non-root` as your `ENTRYPOINT`

Using `run-non-root` as the `ENTRYPOINT` of your container overcomes the downsides of the aforementioned approaches.

```
FROM alpine:3.8

...

ADD https://raw.githubusercontent.com/creemama/run-non-root/master/run-non-root.sh /usr/local/bin/run-non-root
RUN chmod +x /usr/local/bin/run-non-root

ENTRYPOINT ["run-non-root"]
CMD ["--help"]
```

With this approach, you do not have to specify `USER` in your Dockerfile or use the `--user` option when calling `docker run`. Your container runs as a non-root user by default.

If `run-non-root` creates the non-root user (which is nonrootuser by default), this user will have a home directory, and `whoami` will return that user's name.

To run as yourself, specify `-g $(id -g) -u $(id -u)` as arguments to `run-non-root`.

```sh
docker run -it --rm \
  --volume $(app):/app \                   # Mount the source code.
  --workdir /app \                         # Set the working dir.
  my-docker/my-build-environment:latest \  # The build image
  -g $(id -g) -u $(id -u) -- \             # Run as the given user.
  make assets                              # The command
```

## Thank you, `su-exec`

We use [`su-exec`](https://github.com/ncopa/su-exec/tree/dddd1567b7c76365e1e0aac561287975020a8fad) to execute commands so that the command given to `run-non-root` does not run as a child of `run-non-root`; the command [replaces](https://linux.die.net/man/3/exec) `run-non-root`.

We use it over `gosu` since `su-exec` does more or less exactly the same thing as `gosu`, but it is only 10 kilobytes instead of 1.8 megabytes; in fact, `gosu` recommends using `su-exec` over itself in its [installation instructions for Alpine Linux](https://github.com/tianon/gosu/blob/caa402be6661f65c93d63bc205bc36ce055558bf/INSTALL.md).

## `tini`

Use `run-non-root` in conjunction with [`tini`](https://github.com/krallin/tini) to handle zombie reaping and signal forwarding.

```
FROM alpine:3.8

...

ADD https://raw.githubusercontent.com/creemama/run-non-root/master/run-non-root.sh /usr/local/bin/run-non-root
RUN chmod +x /usr/local/bin/run-non-root

ENV TINI_VERSION v0.18.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /bin/tini
RUN chmod +x /bin/tini

ENTRYPOINT ["run-non-root", "--", "tini", "--"]
CMD ["/your/program", "-and", "-its", "arguments"]
```
