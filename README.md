Fig-Docker
----------

Running the fig and docker clients inside of a container, with [Boot2Docker](http://boot2docker.io)

The fig command requires python, which is an extra install for Windows or even CoreOS hosts.

The docker command seems redundant, but it allows you to use fig to start docker containers that require dynamically generated environment variables or parameters outside the pervue of fig.

This is available on docker hub as the image [ianblenke/fig-docker](https://registry.hub.docker.com/u/ianblenke/fig-docker/), and on github as the repository [ianblenke/docker-fig-docker](https://github.com/ianblenke/docker-fig-docker)


What is Fig ?
-------------

[Fig](https://www.orchardup.com/blog/fig) stands for Fast, isolated development environments using Docker, and is now a core orchestration project as [github.com/docker/fig](http://github.com/docker/fig).

Usage:
------

First, create a fig.yml file as per the [Fig](https://www.orchardup.com/blog/fig) documentation.

Make sure you've sourced the boot2docker shellinit for `DOCKER_HOST`, etc:

    eval $(boot2docker shellinit 2>/dev/null)

Running fig inside the container is possible using:

    docker run -v $(pwd):/app -v $DOCKER_CERT_PATH:/certs -e DOCKER_CERT_PATH=/certs -e DOCKER_HOST=$DOCKER_HOST -e DOCKER_TLS_VERIFY=$DOCKER_TLS_VERIFY -ti --rm ianblenke/fig-docker fig --help

It might help to alias it:

    alias fig="docker run -v $(pwd):/app -v $DOCKER_CERT_PATH:/certs -e DOCKER_CERT_PATH=/certs -e DOCKER_HOST=$DOCKER_HOST -e DOCKER_TLS_VERIFY=$DOCKER_TLS_VERIFY -ti --rm ianblenke/fig-docker fig"

Now you can run fig as if you were running it locally:

    fig --help

What if I'm not using boot2docker?
----------------------------------

If you're not using boot2docker, you don't need all of that `DOCKER_HOST` silliness. Instead, you can volume mount the docker.sock from your dockerd:

    docker run -v $(pwd):/app -v /var/run/docker.sock:/var/run/docker.sock -ti --rm ianblenke/fig-docker fig --help

so this alias would work for you:

    alias fig="docker run -v $(pwd):/app -v /var/run/docker.sock:/var/run/docker.sock -ti --rm ianblenke/fig-docker fig"

How do I even docker?
---------------------

Yo dawg. This:

    docker run -v $(pwd):/app -v $DOCKER_CERT_PATH:/certs -e DOCKER_CERT_PATH=/certs -e DOCKER_HOST=$DOCKER_HOST -e DOCKER_TLS_VERIFY=$DOCKER_TLS_VERIFY -ti --rm ianblenke/fig-docker docker ps -a

![I heard you like docker...](http://cdn.meme.am/instances/500x/47024890.jpg)


Boot2Docker Shared Folder
-------------------------

In the above examples, the current directory is being mounted as /app inside the docker container.

On a boot2docker install, the boot2docker VM is the actual source of that volume.

That means you would actually have to have the current path inside the boot2docker VM.

To do that, on a Mac, do this:

    boot2docker down
    VBoxManage sharedfolder add boot2docker-vm -name home -hostpath /Users
    boot2docker up

Now your boot2docker VM should have your home directory mounted as /Users and the path should be the same.

There is likely a similar trick for Windows. I will update this README once I have a chance to test it.

