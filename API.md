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
    MYSQL_USER=user

## Engine starts container and runs the service

    $ ./bin/run

## Other commands

### Client connection

    $ ./bin/client

# Multi-component services

If a service requires more than one process (e.g. hipache), the query script can respond to `components` and provide a list of component names.

    $ ./bin/query components
    hipache
    redis

Then, when running the service, the engine should pass the component name to the `run` script, like this:

    $ ./bin/run hipache
    $ ./bin/run redis

