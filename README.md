# statik

[![Build Status](https://travis-ci.org/mitjaziv/statik.svg?branch=master)](https://travis-ci.org/mitjaziv/statik)

statik allows you to embed a directory of static files into your Go binary to be later served from an http.FileSystem.

This project is fork from [rakyll/statik](http://github.com/rakyll/statik) with some changes:
- Go 1.13.x compatible
- Assets are loaded as string instead of Register func

## Usage

Install the command line tool first.

	go get github.com/mitjaziv/statik

statik is a tiny program that reads a directory and generates a source file that contains its contents. The generated source file registers the directory contents to be used by statik file system.

The command below will walk on the public path and generate a package called `statik` under the current working directory.

    $ statik -src=/path/to/your/project/public

The command below will filter only files on listed extensions.

    $ statik -include=*.jpg,*.txt,*.html,*.css,*.js

In your program, all your need to do is to import the generated package, initialize a new statik file system and serve.

~~~ go
import (
	"github.com/mitjaziv/statik/example/statik"
	"github.com/mitjaziv/statik/fs"
)

  // ...

  statikFS, err := fs.New(statik.Assets)
  if err != nil {
    log.Fatal(err)
  }
  
  // Serve the contents over HTTP.
  http.Handle("/public/", http.StripPrefix("/public/", http.FileServer(statikFS)))
  http.ListenAndServe(":8080", nil)
~~~

Visit http://localhost:8080/public/path/to/file to see your file.

You can also read the content of a single file:

~~~ go
import (
	"github.com/mitjaziv/statik/example/statik"
	"github.com/mitjaziv/statik/fs"
)

  // ...

  statikFS, err := fs.New(statik.Assets)
  if err != nil {
    log.Fatal(err)
  }
  
  // Access individual files by their paths.
  r, err := statikFS.Open("/hello.txt")
  if err != nil {
    log.Fatal(err)
  }    
  defer r.Close()
  contents, err := ioutil.ReadAll(r)
  if err != nil {
    log.Fatal(err)
  }

  fmt.Println(string(contents))
~~~

There is also a working example under [example](https://github.com/mitjaziv/statik/tree/master/example) directory, follow the instructions to build and run it.

Note: The idea and the implementation are hijacked from [camlistore](http://camlistore.org/). I decided to decouple it from its codebase due to the fact I'm actively in need of a similar solution for many of my projects.
