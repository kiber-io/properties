[![](https://img.shields.io/github/tag/kiber-io/properties.svg?style=flat-square&label=release)](https://github.com/kiber-io/properties/releases)
[![License](https://img.shields.io/badge/License-BSD%202--Clause-orange.svg?style=flat-square)](https://raw.githubusercontent.com/kiber-io/properties/master/LICENSE)
[![GoDoc](http://img.shields.io/badge/godoc-reference-5272B4.svg?style=flat-square)](http://godoc.org/github.com/kiber-io/properties)

# Overview

properties is a Go library for reading and writing properties files.

It supports reading from multiple files or URLs and Spring style recursive
property expansion of expressions like `${key}` to their corresponding value.
Value expressions can refer to other keys like in `${key}` or to environment
variables like in `${USER}`.  Filenames can also contain environment variables
like in `/home/${USER}/myapp.properties`.

Properties can be decoded into structs, maps, arrays and values through
struct tags.

Comments and the order of keys are preserved. Comments can be modified
and can be written to the output.

The properties library supports both ISO-8859-1 and UTF-8 encoded data.

Starting from version 1.3.0 the behavior of the MustXXX() functions is
configurable by providing a custom `ErrorHandler` function. The default has
changed from `panic` to `log.Fatal` but this is configurable and custom
error handling functions can be provided. See the package documentation for
details.

Read the full documentation on [![GoDoc](http://img.shields.io/badge/godoc-reference-5272B4.svg?style=flat-square)](http://godoc.org/github.com/kiber-io/properties)

## Getting Started

```go
import (
	"flag"
	"github.com/kiber-io/properties"
)

func main() {
	// init from a file
	p := properties.MustLoadFile("${HOME}/config.properties", properties.UTF8)

	// or multiple files
	p = properties.MustLoadFiles([]string{
			"${HOME}/config.properties",
			"${HOME}/config-${USER}.properties",
		}, properties.UTF8, true)

	// or from a map
	p = properties.LoadMap(map[string]string{"key": "value", "abc": "def"})

	// or from a string
	p = properties.MustLoadString("key=value\nabc=def")

	// or from a URL
	p = properties.MustLoadURL("http://host/path")

	// or from multiple URLs
	p = properties.MustLoadURL([]string{
			"http://host/config",
			"http://host/config-${USER}",
		}, true)

	// or from flags
	p.MustFlag(flag.CommandLine)

	// get values through getters
	host := p.MustGetString("host")
	port := p.GetInt("port", 8080)

	// or through Decode
	type Config struct {
		Host    string        `properties:"host"`
		Port    int           `properties:"port,default=9000"`
		Accept  []string      `properties:"accept,default=image/png;image;gif"`
		Timeout time.Duration `properties:"timeout,default=5s"`
	}
	var cfg Config
	if err := p.Decode(&cfg); err != nil {
		log.Fatal(err)
	}
}

```

## Installation and Upgrade

```
$ go get -u github.com/kiber-io/properties
```

## License

2 clause BSD license. See [LICENSE](https://github.com/kiber-io/properties/blob/master/LICENSE) file for details.

## ToDo

* Dump contents with passwords and secrets obscured
