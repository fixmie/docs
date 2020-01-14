# Fixmie documentation

Welcome to the official [Fixmie](https://fixmie.com) documentation. 

# Table of Contents

* [Introduction](#introduction)
* [Fixers](#fixers)
  * [go](#go)
* [Configuration](#configuration)
  * [go](#go-1)
  * [ignore](#ignore)

# Introduction

Fixmie analyzes and automatically fixes your source code by providing smart and useful
suggestions. Fixmie is more powerful than your ordinary linter, it not only
provides useful tips, it also rewrites your source code and suggest the
changes. It's up to you whether to accept these changes or to decline. 

Follow the instructions after authorizing to Github on
[fixmie.com](https://fixmie.com).  Once the Fixmie Github application is
installed to the specified repositories, Fixmie listens for pull requests
events and checks each changed file in that particular pull request. 

Each file is checked by multiple fixers concurrently, independently from each
other. This means there might be one or multiple fixes for a single line.

# Fixers

Fixmie contains multiple fixers for various kind of checks. The current set of
supported fixers are:

## go

The `go` fixer supports the following modes for the [Go programming
language](https://golang.org):

* `comments`: 
  * Rewrites existing comments if first word doesn't match exported identifier
  * Rewrites existing comments first word is `Foo` to `Foo ...`
* `errors`: 
  * Replaces `errors.New(fmt.Sprintf(...))` with `fmt.Errorf(...)`
  * Replaces `t.Error(fmt.Sprintf(...))` with `t.Errorf(...)`
  * Removes following ending characters from error strings:
    * punctuation `.` 
	* colon `:` 
	* newline `\n` 
	* exclamation marks `!`
* `errwrap`: 
  * Wrap and fix Go errors with the new `%w` verb directive. This fixer uses
    the `errwrap` analyzer: https://github.com/fatih/errwrap
* `returns`: 
  * Moves the position of an `error` return if isn't the last parameter of a
	function declaration. I.e: `func foo() (error, int)` is replaced with `func
	foo() (int, error)`. The mode also updates all returns in the function
	declaration.
* `formats`: 
  * Formats and fixes the lines that are not formatted according to gofmt rules. 
* `params`: 
  * Simplifies function parameters and results to omit the type if two
    consecutive types are the same (i.e: `func foo(a int, b int)` -> `func foo(a, b int)`
* `builtins`: 
  * Checks identifier's that shadows a predefined identifier (builtin), 
    such as `new`, `len`, `delete`, etc...  Replaces the identifiers
    and the local usages, i,e: `len := 5` -> `length := 5`.
* `times`: 
  * Improves `time` package usages:
	* simplifies `time.Now().Sub(t)` -> `time.Since(t)`
	* simplifies `t.Sub(time.Now())` -> `time.Until(t)`


The `go` fixer ignores certain types of files and folders:

* `vendor/` folders
* files ending with `.pb.go` or `.gen.go`
* files and folders starting with underscore (i.e: `_foo.go` or `_test-fixtures/`)
* generated files containing the rule `^// Code generated .* DO NOT EDIT\.$`, 
  for more information checkout the definition: [https://golang.org/s/generatedcode](https://golang.org/s/generatedcode)


# Configuration

**IMPORTANT: the configuration file format is not final yet and can be changed
during the Beta stage. Please check this page occasionally to see the breaking
changes.**

You can configure certain aspects of Fixmie by adding the `.fixmie.yml` file to
your repository. An example configuration might look like:

```yaml
go:
  comments:
    disabled: true

ignore:
 - "test_data/"
 - "*.js.go"
```

## go

The `go` field can be used to configure certain aspects of the `go` fixer.
Each field under the `go` field is named according to the mode (see the
[Fixers](#fixers) section). The following example shows all current options:

```yaml
go:
  comments:
    disabled: true
  errors:
    disabled: true
  errwrap:
    disabled: true
  returns:
    disabled: true
  formats:
    disabled: true
  params:
    disabled: true
  builtins:
    disabled: true
  times:
    disabled: true
```

Currently, you can disable each individual mode. For example, defining the
following configuration will disable the `comments` mode for each consecutive
PR's:

```yaml
go:
  comments:
    disabled: true
```

In the future, we're planning to add even deeper customizations for each mode.

## ignore

The `ignore` field can be used to ignore files or folders. It's an array of
strings, where each item should be specified according to the `gitignore`
rules: https://git-scm.com/docs/gitignore

This is a global setting and applies to all fixers.

Example usage:


```yaml
# ignores any file under test_data and files ending with .js.go
ignore:
 - "test_data/"
 - "*.js.go"
```

The `ignore` field is combined with the default ignore list of each fixer.
As an example, adding the following won't have any effect because `vendor/` is
already ignored:

```yaml
# this won't have any effect on the `go` fixer, because 
# these files/folders are already ignored by the `go` fixer.
ignore:
 - "_*.go"
 - "vendor/"
```
