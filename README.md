
Version: 2020.04.24

DESCRIPTION
===========

  This is a simple script intended to ease the execution from the command line
  of commands inside docker containers. It makes the execution behave mostly
  like any other host command. The main characteristics are:

  - Automatic access to user files in the host.
  - Generated files owned by the current user.
  - Support input from stdin and redirection of stderr and stdout.
  - Support of complex command arguments, e.g. spaces, quotes, etc.

  The basic system requirement is that docker must be installed and configured
  to not require sudo.

  A new container is created for each call to docker-cli and
  when execution finishes the container is automatically removed. The command is
  executed as the current user from the current working directory. Executed
  commands will only work if it is executed from and referencing only files in
  the host's /Users, /home, /mnt, /media or /tmp directories.

  The interface can also be used to execute external scripts inside the
  container. For this to work, the script must be located in a directory
  accessible to the container (see above) and be in a directory in your $DPATH
  environment variable or be referenced with its full or relative path.


INSTALLATION
============

  By installation it is meant to make the docker-cli
  command runnable from any location. If you cloned the github repository, it is
  recommended that you create a symlink from the clone to any directory in your
  path, i.e.

    cd $HOME/.local/bin
    ln -s path_to_repo/docker-cli

  Copying the script to any directory in your path would also work.

  **Bash completion:**

  It is very useful to enable bash completion for example to ease typing of
  image names and tags. This can be enabled by sourcing the script, though this
  functionality depends on the docker bash completion, so this one needs to be
  enabled first. To make it permanent you could add to your .bashrc the
  following (adapting to your case):

    source /usr/share/bash-completion/completions/docker
    source $HOME/.local/bin/docker-cli


SYNOPSIS
========

    docker-cli [OPTIONS] -- DOCKER_IMAGE COMMAND ARGUMENTS
    docker-cli [OPTIONS] -- DOCKER_IMAGE bash   # Prompt becomes USER_ID@CONTAINER_ID$


OPTIONS
=======

    --user UID:GID            (default: current user and current directory's group)
    --runtime=DOCKER_RUNTIME  (e.g. nvidia)
    --ipc=DOCKER_IPC          (e.g. host)
    --publish=LIST            (e.g. 8080:8080)
    --cli-debug               Prints the docker run command right before execution.
    --help                    Prints this help message and exits.
    --version                 Prints the version of docker-cli and exits.

  In general most of the options that docker run accepts should work. For more
  details check docker run --help.


EXAMPLES
========

    # Get the version of some command in the container
    docker-cli -- ubuntu:16.04 sed --version

    # Container command as part of a pipe
    echo 'hello world!' | docker-cli -- ubuntu:16.04 grep hello | xargs echo

    # Create files in container
    echo 'hello world!' | docker-cli -- ubuntu:16.04 tee test.txt
    ls -l test.txt
    rm test.txt

    # Use of nvidia GPU in container
    docker-cli --runtime=nvidia -- nvidia/cuda:10.1-runtime-ubuntu16.04 nvidia-smi

    # Run a jupyter notebook in container
    docker-cli --publish=8888:8888 -- DOCKER_IMAGE bash
    export HOME=$(pwd)
    jupyter notebook --ip 0.0.0.0 --no-browser

