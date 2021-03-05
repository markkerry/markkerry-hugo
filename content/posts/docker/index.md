---
title: "Go Web App in Docker"
date: 2021-03-05T14:35:09Z
draft: true
---

## Folder structure

```terminal
/dockerfile
/go/src/app/main.go
```

## dockerfile

```docker
FROM golang:1.14

WORKDIR /go/src/app
COPY . .

RUN go get -d -v ./...
RUN go install -v ./...

CMD ["app"]
```

## main.go

```go
package main

import (
  "fmt"
  "net/http"
  "os"
)

func homePage(w http.ResponseWriter, r *http.Request) {
    hostname, err := os.Hostname()
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
    fmt.Fprintf(w, "Go App running on host: %s\n", hostname)
}

func setupRoutes() {
    http.HandleFunc("/", homePage)
}

func main() {
    hostname, err := os.Hostname()
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
    fmt.Printf("Go Web App Started on Port 3000 on host: %s\n", hostname)
    setupRoutes()
    http.ListenAndServe(":3000", nil)
}
```

## Build the Container

```bash
docker build -t go-app .
```

![dockerBuild](images/dockerBuild.jpg)

## Run the container

Delete the container after it is stopped

```bash
docker run -it --rm --name running-app go-app
```

![dockerRun](images/dockerRun.jpg)

Now browse to http://localhost:3000 to see the running container in your browser
