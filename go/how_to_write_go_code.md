# Code organization

**Go programs are organized into packages**

- Package is a collection of files in the same directory, that are compiled together.
- Functions, types, variables, and constants defined in one source file are visible to other within the same package.

**A repository contains one or more modules**

- A module is a collection of packages that are released together
- Go repository typically contains only one module, located at the root
- A file named `go.mod` declares the module path: the import path prefix for all packages within the module
- The module contains the packages in the directory containing its `go.mod` file as well as subdirectories of that directory, up to the next subdirectory containing another `go.mod` file (if any)

Each module's path not only serves as an import path prefix for its packages, but also indicates where the go command should look to download it. For example, to download the module `golang.org/x/tools`, the `go` command would consult the repository indicated by `https://golang.org/x/tools`

- An import path is a string used to import a package
- A package's import path is its module path joined with its subdirectory within the module. For example, the module `github.com/google/go-cmp` contains a package in the directory `cmp/`. That package's import path is `github.com/google/go-cmp/cmp`
- Packages in the standard library do not have a module path prefix.

# Your first program

```bash
$ mkdir hello
$ cd hello
$ go mod init example.com/user/hello
go: creating new go.mod: module example.com/user/hello
$ cat go.mod
module example.com/user/hello

go 1.14
```

The first statement in a Go source file must be package name. Executable commands must always use package main.

Next, create a file named hello.go inside that directory containing the following Go code:

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, world.")
}
```

## Go install command

Now you can build and install that program with the go tool:

`go install example.com/user/hello`

This command builds the hello command, producing an executable binary. It then installs that binary as `$HOME/go/bin/hello`

- The install directory is controlled by the `GOPATH` and `GOBIN` environment variables.
- If `GOBIN` is set, binaries are installed to that directory.
- If `GOPATH` is set, binaries are installed to the `bin` subdirectory of the first directory in the `GOPATH` list.
- Otherwise, binaries are installed to the `bin` subdirectory of the default `GOPATH` (`$HOME/go`)

You can use the go env command to portably set the default value for an environment variable for future go commands:

`$ go env -w GOBIN=/somewhere/else/bin`

To unset a variable previously set by go env -w, use go env -u:

`$ go env -u GOBIN`

Commands like `go install` apply within the context of the module containing the current working directory. If the working directory is not within the `example.com/user/hello` module, `go install` may fail.

For convenience, go commands accept paths relative to the working directory, and default to the package in the current working directory if no other path is given. So in our working directory, the following commands are all equivalent:

```bash
$ go install example.com/user/hello
$ go install .
$ go install
```

Next, let's run the program to ensure it works. For added convenience, we'll add the install directory to our `PATH` to make running binaries easy:

```bash
$ export PATH=$PATH:$(dirname $(go list -f '{{.Target}}' .))
$ hello
Hello, world.
```
