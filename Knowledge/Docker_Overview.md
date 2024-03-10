

Docker is an <mark style="background: #ADCCFFA6;">open platform</mark> for developing, shipping, and running applications. <mark style="background: #FFB86CA6;">Docker enables you to separate your applications from your infrastructure so you can deliver software quickly.</mark> With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker's methodologies for shipping, testing, and deploying code, you can <mark style="background: #BBFABBA6;">significantly reduce the delay between writing code and running it in production</mark>.

## [Docker architecture](https://docs.docker.com/get-started/overview/#docker-architecture)

Docker uses a <mark style="background: #FFB8EBA6;">client-server architecture</mark>. The Docker <mark style="background: #D2B3FFA6;">client talks to the Docker daemon</mark>, which does the heavy lifting of building, running, and distributing your Docker containers. <mark style="background: #ADCCFFA6;">The Docker client and daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon.</mark> The Docker <mark style="background: #FFB86CA6;">client and daemon communicate using a REST API, over UNIX sockets or a network interface</mark>. Another Docker client is Docker Compose, that lets you work with applications consisting of a set of containers.

![[docker-architecture-img.png]]

### [The Docker daemon](https://docs.docker.com/get-started/overview/#the-docker-daemon)

The Docker daemon (`dockerd`) listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes. A daemon can also communicate with other daemons to manage Docker services.

<mark style="background: #D2B3FFA6;">That why we usually use -d for run docker container.</mark>

### [Docker Desktop](https://docs.docker.com/get-started/overview/#docker-desktop)

Docker Desktop is an easy-to-install application for your Mac, Windows or Linux environment that enables you to build and share containerized applications and microservices. <mark style="background: #ADCCFFA6;">Docker Desktop includes the Docker daemon (`dockerd`), the Docker client (`docker`), Docker Compose, Docker Content Trust, Kubernetes</mark>, and Credential Helper. For more information, see [Docker Desktop](https://docs.docker.com/desktop/).

### [Docker registries](https://docs.docker.com/get-started/overview/#docker-registries)

A Docker registry stores Docker images. <mark style="background: #FFB8EBA6;">Docker Hub is a public registry that anyone can use</mark>, and Docker looks for images on Docker Hub by default. You can even run your own private registry.

When you use the `docker pull` or `docker run` commands, Docker pulls the required images from your configured registry. When you use the `docker push` command, <mark style="background: #FFB8EBA6;">Docker pushes your image to your configured registry</mark>.

### [Docker objects](https://docs.docker.com/get-started/overview/#docker-objects)

When you use Docker, you are creating and using images, containers, networks, volumes, plugins, and other objects. This section <mark style="background: #FFB86CA6;">is a brief overview of some of those objects.
</mark>

#### [Images](https://docs.docker.com/get-started/overview/#images)

An<mark style="background: #D2B3FFA6;"> image is a read-only template with instructions for creating a Docker container</mark>. Often, an image is based on another image, with some additional customization. For example, you may build an image which is based on the `ubuntu` image, but installs the Apache web server and your application, as well as the configuration details needed to make your application run.

<mark style="background: #D2B3FFA6;">You might create your own images or you might only use those created by others and published in a registry.</mark> To build your own image, you create a [Dockerfile](Dockerfile_CLI) with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a [Dockerfile](Dockerfile_CLI) creates a layer in the image. <mark style="background: #FFB8EBA6;">When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt</mark>. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

#### [Containers](https://docs.docker.com/get-started/overview/#containers)

<mark style="background: #FFB86CA6;">A container is a runnable instance of an image.</mark> You can create, start, stop, move, or delete a container using the Docker API or CLI. You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.

<mark style="background: #FFB86CA6;">By default, a container is relatively well isolated from other containers and its host machine</mark>. You can control how isolated a container's network, storage, or other underlying subsystems are from other containers or from the host machine.

A container is defined by its image as well as any configuration options you provide to it when you create or start it. <mark style="background: #BBFABBA6;">When a container is removed, any changes to its state that aren't stored in persistent storage disappear.</mark>

##### [Example docker run command](https://docs.docker.com/get-started/overview/#example-docker-run-command)

The following command runs an `ubuntu` container, attaches interactively to your local command-line session, and runs `/bin/bash`.

```console
$ docker run -i -t ubuntu /bin/bash
```

When you run this command, the following happens (assuming you are using the default registry configuration):

1. If you don't have the `ubuntu` image locally, Docker pulls it from your configured registry, as though you had run `docker pull ubuntu` manually.
    
2. Docker creates a new container, as though you had run a `docker container create` command manually.
    
3. Docker allocates a read-write filesystem to the container, as its final layer. This allows a running container to create or modify files and directories in its local filesystem.
    
4. Docker creates a network interface to connect the container to the default network, since you didn't specify any networking options. This includes assigning an IP address to the container. By default, containers can connect to external networks using the host machine's network connection.
    
5. Docker starts the container and executes `/bin/bash`. Because the container is running interactively and attached to your terminal (due to the `-i` and `-t` flags), you can provide input using your keyboard while Docker logs the output to your terminal.
    
6. When you run `exit` to terminate the `/bin/bash` command, the container stops but isn't removed. You can start it again or remove it.

