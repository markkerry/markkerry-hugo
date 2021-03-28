---
title: "Running a Local Go Web App in Docker"
date: 2021-03-14T14:35:09Z
draft: false
tags: ["docker", "Go"]
cover:
    image: "images/cover.png"
    alt: "<alt text>"
    caption: "Create a simple Go web app that runs in a Docker container"
    relative: false
---

In this post, I walk through creating a simple Go web app that runs in a Docker container, and lists the hostname of the container it's running in. I chose Go as I've recently gotten started writing code in Go and it's a lot of fun to work with.

The container will install Go, so there is no need to install it locally on your machine. Docker is a prerequisite, however.

First, lets take a look at the folder structure of where to keep your `dockerfile` and `main.go` code.

<br>

## Folder Structure

```terminal
~/dockerfile
~/go/src/app/main.go
```

<br>

## dockerfile

The dockerfile contains the instruction of how to build our docker image and the app to run when the container is created from the image. Let's start by creating the dockerfile:

```teminal
cd ~
touch dockerfile
```

Here I referenced the following [Docker/Golang](https://hub.docker.com/_/golang) official image documentation. Using your favourite code editor, modify the contents of the dockerfile to look as follows:

```docker
FROM golang:1.14

WORKDIR /go/src/app
COPY . .

RUN go get -d -v ./...
RUN go install -v ./...
EXPOSE 3000
CMD ["app"]
```

<br>

## main.go

Now let's create the main.go file and the folder structure to store it.

```termial
mkdir ~/go
mkdir ~/go/src
mkdir ~/go/src/app
touch ~/go/src/app/main.go
```

Here is the contents of the main.go file:

```go
package main

import (
  "fmt"
  "net/http"
  "os"
)

func homePage(w http.ResponseWriter, r *http.Request) {
    // get the hostname of the container
    hostname, err := os.Hostname()
    if err != nil {
        fmt.Println(err)
        os.Exit(1)
    }
    // print the hostname of the container
    fmt.Fprintf(w, "Go App running on host: %s\n", hostname)
}

func setupRoutes() {
    http.HandleFunc("/", homePage)
}

func main() {
    // print to terminal if container run with -it flag
    fmt.Printf("Go Web App Started on Port 3000\n")
    setupRoutes()
    http.ListenAndServe(":3000", nil)
}
```

<br>

## Build the Container

To build the container, ensure you are in the `~/` directory which hosts your dockerfile, then type the following.

```bash
docker build -t go-app .
```

![dockerBuild](images/dockerBuild.jpg)

Let's take a look at the container image by running:

```terminal
docker image ls
```

I have two docker containers in my example. The one we just created is called __go-app__ with an _IMAGE ID_ of __8128cf5f8047__.

![dockerImage](images/dockerImage.jpg)

<br>

## Run the Container

Finally, let's run the container from the image, but ensure we automatically delete the container after it is stopped. The `-d` flag will ensure it runs detached (non-interactive), the `-rm` flag will remove the container when it is stopped, and the `-p` flag will map local port 3000 to port 3000 in the container

```bash
docker run -d --rm -p 3000:3000 --name running-app go-app
```

Let's run the curl command to see what happens

```bash
curl localhost:3000

Go App running on host: 18987a97c027
```

Now browse to http://localhost:3000 to see the running container in your browser

![web](images/web.jpg)

Running `docker container ls` will display the running container and it's port mapping below.

```bash
CCONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
18987a97c027        go-app              "app"               13 seconds ago      Up 12 seconds       0.0.0.0:3000->3000/tcp   running-app
```

<br>

## Wrapping Up

To stop the container, simply run

```bash
docker container stop running-app
```

If you ran the container with the `-it` (interactive) flag instead of `-d` (detached), press `ctrl + c` to cancel out of the interactive container session.

Running `docker container ls` will show the container is no longer running and was also deleted after it stopped.

If you want to delete the image, get the IMAGE ID by running `docker image ls`, then running the following (8128cf5f8047 in my example is the IMAGE ID):

```terminal
docker image rm 8128cf5f8047
```

Next I plan the create a container which does something else than simply run a web page stating the host of the container it is running in. But this does feel like a nice first step when progressing towards Docker Swarm or Kubernetes. Also, I will hopefully look to deploy to an Azure Container Instance or Azure Kubernetes Service.
