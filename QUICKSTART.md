# Quickstart

There are three phases for using servicepacks:

1. Building the image
2. Running the service container
3. Interacting with the running service

## Building a service image

Building a service image happens in two phases:

1. Build the base image with the service software.  This image will be the base for all services of this type.
1. Setup an image instance.

### Build the base image

Pass in the servicepack directory and the docker repo and tag to use.

    $ svp build -b pack_dir -t repo[:tag]

Example:

    $ git clone https://github.com/pocketpaas/servicepack_mysql.git
    $ svp build -b servicepack_mysql -t mysql
    ...
    $ docker images
    REPOSITORY           TAG         ID                  CREATED             SIZE
    mysql                latest      f42b0cb83738        2 seconds ago       8.678 MB (virtual 200 MB)

### Setup a service instance based on the base image

Pass in the servicepack directory, the base image, and the docker repo and tag to use.  The output will be the environment variables that should 

    $ svp setup -b pack_dir -i baserepo[:tag] -t repo[:tag]

Example:

    $ svp setup -i mysvc -b servicepack_mysql -t mysql
    MYSQL_PASSWORD=3fjq3ifjalwj
    MYSQL_DATABASE=db
    MYSQL_USER=user

## Running the service container

The svp script doesn't handle running the actual service container.  To do that, use `docker run` with the following additional information:

* the environment variables that were the output of the `svp setup` run
* expose the ports that `svp ports` reveals
* expose the ssh port so that the service management commands will work

So, to start the above mysql container, those correspond to:

* `-e MYSQL_PASSWORD=3fjq3ifjalwj -e MYSQL_DATABASE=db -e MYSQL_USER=user`
* `-p 3306`
* `-p 127.0.0.1::22`

The docker run command looks like this:

    $ docker run -d -e MYSQL_PASSWORD=3fjq3ifjalwj -e MYSQL_DATABASE=db -e MYSQL_USER=user -p 3306 -p 127.0.0.1::22 mysql

## Interacting with the running service

Once the service is running, `svp` can be used to interact with the running service container

### Client

This runs a client in the running container for the service.

    $ svp client -c 0fb2c55748aa
    Warning: Permanently added '[localhost]:49153' (ECDSA) to the list of known hosts.
    mysql> 

### Shell

This runs a shell in the running container for the service.

    $ svp shell -c 0fb2c55748aa
    Warning: Permanently added '[localhost]:49153' (ECDSA) to the list of known hosts.
    Welcome to Ubuntu 12.04 LTS (GNU/Linux 3.8.0-32-generic x86_64)
    
     * Documentation:  https://help.ubuntu.com/
    Last login: Thu Nov 28 20:49:56 2013 from 172.17.42.1
    root@0fb2c55748aa:~# 

