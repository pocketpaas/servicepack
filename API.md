# Workflow

## Engine queries OS

    $ ./bin/query os
    ubuntu/13.04

## Engine queries secret requirements

    $ ./bin/query secrets
    MYSQL_PASSWORD

## Engine spins up container with contents of servicepack and then runs the build script inside.

    $ ./bin/build

This script should do everything to set up the service, including installing packages and setting configuration.

Environment variables corresponding to the generated secrets will be present.  For example if the secret 'MYSQL\_PASSWORD' is requested, then MYSQL\_PASSWORD will be in the environment with a generated password inside.

### Phased build

The build can happen in two phases, if desired.  The phase is passed in as an argument to the build script.

* `all` - If this is specified, then both the base setup and the custom setup for this service should be performed.  This is the default.
* `base` - Only do what is required for all services of this type.  If this is specified, then no secret environment variables will be passed.
* `setup` - Only do the setup for this specific instance of the service.

## Engine queries additional environment variables

These variables can contain special keys to instruct the engine where to insert container information, such as %IP for the ip address.

    $ ./bin/query env
    MYSQL_PORT=3306
    MYSQL_USER=user
    MYSQL_HOST=%IP

## Engine starts container and runs the service

    $ ./bin/run

## Other commands

### Backup

    $ ./bin/backup

### Client connection

    $ ./bin/client

### Extra

    $ ./bin/extra help
    foo: does the foo
    baz: also baz is possible

    $ ./bin/extra foo \[args]

# Multi-component services

If a service requires more than one process (e.g. hipache), the query script can respond to `components` and provide a list of component names.

    $ ./bin/query components
    hipache
    redis

Then, when running the service, the engine should pass the component name to the `run` script, like this:

    $ ./bin/run hipache
    $ ./bin/run redis

# Running 'servicepack'

## Building a service

Building a service happens in two phases:

1. Build the base image with the service software.  This image will be the base for all services of this type.
1. Setup an image instance.

### Build the base image

Pass in the servicepack directory and the docker repo and tag to use.

    $ svp build -b pack_dir -t repo[:tag]

Example:

    $ git clone https://github.com/pocketpaas/servicepack-mysql.git
    $ svp build -b servicepack-mysql -t mysql
    ...
    $ docker images
    REPOSITORY           TAG         ID                  CREATED             SIZE
    mysql                latest      f42b0cb83738        2 seconds ago       8.678 MB (virtual 200 MB)

### Setup a service instance based on the base image

Pass in the servicepack directory, the base image, and the docker repo and tag to use.  The output will be the environment variables that should 

    $ svp setup -b pack_dir -i baserepo[:tag] -t repo[:tag]

Example:

    $ svp setup -i mysvc -b servicepack-mysql -t mysql
    MYSQL_PASSWORD=3fjq3ifjalwj
    MYSQL_PORT=3306
    MYSQL_DATABASE=db
    MYSQL_USER=user
    MYSQL_HOST=%IP
    ...
    $ docker images
    REPOSITORY           TAG         ID                  CREATED             SIZE
    mysql                latest      f42b0cb83738        2 minutes ago       8.678 MB (virtual 200 MB)
    mysvc                latest      24b0fcb38877        2 seconds ago       8.678 MB (virtual 200 MB)
