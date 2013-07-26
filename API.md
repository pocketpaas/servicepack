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

## Engine queries additional variables

    $ ./bin/query env
    MYSQL_PORT=3306

## Engine starts container and runs the service

    $ ./bin/run
