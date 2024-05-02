# Jenkins Shared Lib. Example

## Purpose
To demonstrate the use of a Jenkins Shared library and how it resolves classes / imports.

## Tags
This repo makes use of different tags to demonstrate different configurations.
You can do a git checkout of any tag by running `git checkout <TAG_NAME>`.

### Baseline
The tag `baseline` points to the baseline commit where the library works with a single defined class, a single file in vars, and a single stage in the `Jenkinsfile` which calls the method from `vars/common.groovy` to print `"Hello, World"`.