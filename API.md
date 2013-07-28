# Workflow

## Engine queries OS

    $ ./bin/query os
    ubuntu/13.04

## Engine queries secret requirements

    $ ./bin/query secrets
    mysql_root_password length=12
    mysql_user_password length=12

## Engine spins up container with contents of servicepack and then runs the build script inside.

    $ ./bin/build

This script should do everything to set up the service, including installing packages and setting configuration.

Environment variables corresponding to the generated secrets will be present.  For example if the secret 'mysql\_root\_password' is requested, then MYSQL\_ROOT\_PASSWORD will be in the environment with a generated password inside.

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

# Running 'servicepack'

## Building a service

Pass in the servicepack directory and the docker repo and tag to use.  The output will be the environment variables that should 

    $ servicepack build pack_dir repo [tag]

Example:

    $ git clone https://github.com/pocketpaas/servicepack-mysql.git
    $ servicepack build servicepack-mysql mysql
    MYSQL_ROOT_PASSWORD=ifjalfjalqwj
    MYSQL_USER_PASSWORD=3fjq3ifjalwj
    MYSQL_PORT=3306
    MYSQL_USER=user
    MYSQL_HOST=%IP
    $ docker images
    REPOSITORY           TAG         ID                  CREATED             SIZE
    servicepack/mysql    latest      f42b0cb83738        2 seconds ago       8.678 MB (virtual 200 MB)

