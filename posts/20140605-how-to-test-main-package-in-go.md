<!--
.. title: How to Test Package Main in Go
.. slug: how-to-test-package-main-in-go
.. date: 2014/06/05 01:00:00
.. tags: go
.. link:
.. description:
-->

There are a few things to get right when trying to test the main package in Go.
This is a result of package naming rules and where files need to be located. My
attempts from other testing frameworks (such as Python) led me to error messages
such as:

- `cannot find package "mypackage" in any of: /first/path/ (from $GOROOT), /second/path/ (from $GOPATH)`
- `can't load package: package mypackage: found packages main (mypackage.go) and mypackage (mypackage_test.go) in /project/path`
- `./mypackage_test.go:4: import "path/to/mypackage" while compiling that package (import cycle)`

However, it should be possible to resolve all these errors by following
this checklist:

- An executable package must have the name `main` (so `package main`).
- The name of the code file can be as desired, so `example.go` in this example.
- The name of the test file should have `_test.go` at the end, so `example_test.go`.
- The test file should be in the same directory as the code file.
- The package name of the test file must also be `package main`.
- There is no need to import the code in the test file as everything is in the same package.

You don't necessarily need to have these files in a subdirectory of your project; you can
have all files in the root directory of the project.
