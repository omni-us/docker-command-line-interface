
Version: 2018.12.10

DESCRIPTION
===========

  This is a simple script intended to ease the execution from the command line of
  commands inside docker containers. It makes the execution behave mostly like any other host command. The main characteristics are:
  - Automatic access to user files in the host.
  - Generated files owned by the current user.
  - Support input from stdin and redirection of stderr and stdout.
  - Support of complex command arguments, e.g. spaces, quotes, etc.

  The basic system requirement is that docker must be installed and configured to
  not require sudo.

  A new container is created for each call to docker-command-line-interface and when
  execution finishes the container is automatically removed. The
  command is executed as the current user from the current working directory.
  Executed commands will only work if it is executed from and referencing only
  files in the host's /Users, /home, /mnt, /media or /tmp directories.

  The interface can also be used to execute external scripts inside the container.
  For this to work, the script must be located in a directory accessible
  to the container (see above) and be in your $DPATH environment variable or be
  referenced with its full or relative path.


SYNOPSIS
========

  docker-command-line-interface [OPTIONS] -- DOCKER_IMAGE bash  
  docker-command-line-interface [OPTIONS] -- DOCKER_IMAGE COMMAND ARGUMENTS


OPTIONS
=======

  --user UID:GID            (default: current user and current directory's group)  
  --runtime=DOCKER_RUNTIME  (e.g. nvidia)  
  --ipc=DOCKER_IPC          (e.g. host)  
  --publish=LIST            (e.g. 8080:8080)

  In general most of the options that docker run accepts should work. For more
  details check docker run --help.


EXAMPLES
========

    # Get the version of some command in the container
    docker-command-line-interface -- ubuntu:16.04 sed --version

    # Container command as part of a pipe
    echo 'hello world!' | docker-command-line-interface -- ubuntu:16.04 grep hello | xargs echo

    # Create files in container
    echo 'hello world!' | docker-command-line-interface -- ubuntu:16.04 tee test.txt
    ls -l test.txt
    rm test.txt

    # Use of nvidia GPU in container
    docker-command-line-interface --runtime=nvidia -- nvidia/cuda:8.0-cudnn5-devel-ubuntu16.04 nvidia-smi

    # Run a jupyter notebook in container
    docker-command-line-interface --publish=8888:8888 -- DOCKER_IMAGE bash
    export HOME=/home/mvillegas/repos/github/docker-command-line-interface
    jupyter notebook --ip 0.0.0.0 --no-browser

