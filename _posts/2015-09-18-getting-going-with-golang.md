---
layout: post
title:  Getting going with Golang
date:   2015-09-18 11:41:00
---

Lately I have been learning Go. Every so often I get put on another project, so then get distracted with something else. I'm hoping to do a series of posts on Go to try and ingrain the language in my brain a bit better.

First topic is setting up the environment on my local Ubuntu based machine. The first task you need to do is install Go. This can be done through the metapackage `golang`.

```bash
$ apt-cache search --names-only golang$
dh-golang - debhelper add-on for packaging software written in Go (golang)
golang - Go programming language compiler - metapackage
$ apt-cache policy golang
golang:
  Installed: (none)
  Candidate: 2:1.2.1-2ubuntu1
  Version table:
 *** 2:1.2.1-2ubuntu1 0
        500 http://au.archive.ubuntu.com/ubuntu/ trusty/universe amd64 Packages
        100 /var/lib/dpkg/status

$ sudo apt-get install golang
```

As you can see, the candidate version is 1.2. However golang is currently upto version 1.5. So if you want the latest features, you can grab the tarball from the downloads page[[1](https://golang.org/dl/)], and then follow the steps as described on the getting started guide[[2](https://golang.org/doc/install)] to install it.

If the installation is successful, you should be able to verify by running

```bash
$ go version
go version go1.2.1 linux/amd64
```
Once you have go installed you will want to set up a couple of environment variables. First, you need to set GOPATH to the location you will be placing all your go source code. For me, I have a Projects folder in my homedir, then a go folder (~/Projects/go).

The other environment variable you will want to update is the PATH such that all your built binaries will be available to you. Whenever you build a go source file, the binary file goes into `$GOPATH/bin`. So, I add the following two lines to my .bashrc file:

```bash
export GOPATH=/home/trent/Projects/go
export PATH=$PATH:$GOPATH/bin
```

Your GOPATH will then typically contain 3 folders:

1. bin - any source files you compile with a main function will land in here
* pkg - any libraries/dependencies you compile will land in here. If
* src - all your source files will land here. You will likely want to associate packages to your github profile, so will have the directory tree: github.com/tschf/projectName

nb: the pkg and bin directories will automatically be created as needed.

To test this out, we will set up the directory structure - then create a project named helloworld:

```bash
$ cd $GOPATH
$ mkdir -p src/github.com/tschf
$ mkdir helloworld
```

In there, we add the source file `main.go` with the following code:

```go
package main

import "fmt"

func main(){
    fmt.Printf("hello world\n")
}
```

Then to compile our application, from that same directory we can run `go install` or from anywhere on our filesystem, we can run `go install github.com/tschf/helloworld`. After that you will find the binary `helloworld` located in `$GOPATH/bin`.

Or, at any time you can run a go source file without prior compilation by running the command `go run <file.go>`.


Another package you will want to install is the `golang-go.tools` package. In particular, this includes the `godoc` utility that allows you to view the documentation for go packages, from the terminal. e.g. `godoc net/http`

More topics:

* [Go variable basics](/2015/09/24/go-variable-basics/)
* [Go functions and methods](/2015/10/10/go-functions-methods)
* [Go structs and interfaces](/2015/10/11/go-structs-interfaces)
* [Go list structures](/2015/10/13/go-list-structures/)


1. [https://golang.org/dl/](https://golang.org/dl/)
*  [https://golang.org/doc/install](https://golang.org/doc/install)
