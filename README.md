# Jenkins Shared Lib. Example

## Purpose
This repo demonstrates the use of a Jenkins Shared library and how it resolves classes / imports.

The 3 main files in this repo are:
1. `Jenkinsfile`is the main entrypoint where Jenkins starts running a Pipeline.
2. `vars/common.groovy` exposes functions from the shared lib. so they can be called from a stage in `Jenkinsfile`.
3. `src/com/brendenhyde/Foo.groovy` is a shared library file where Groovy classes are defined.

The structure of this repo's Jenkins Shared Library follows the [official Jenkins documentation](https://www.jenkins.io/doc/book/pipeline/shared-libraries/#directory-structure) for defining a shared library.

I tested all of these configurations on Jenkins version `2.452-jdk17`.

## Tags
This repo makes use of different tags to demonstrate different configurations.

All tags marked with a ✅ have been successfully run on Jenkins and demonstrate various ways to structure your Jenkins shared library code.

All tags marked with a ❌ have caused Jenkins pipelines to crash and demonstrate what _not_ to do.

You can switch to any tag by running `git checkout <TAG_NAME>`.
After you've finished, you can do a `git checkout main` to return to the latest commit of the repo.

### baseline ✅
The `baseline` tag points to the baseline commit where the library works with a single defined class, a single file in `vars` called `common.groovy`, and a single stage in the `Jenkinsfile` which calls the method `useFoo()` from `vars/common.groovy` to print `"Hello, World"`.

### two-in-one ❌
The `two-in-one` tag shows what happens when two classes are defined in the same `.groovy` file where one of the classes matches the filename.

Based on my testing, this configuration crashes Jenkins, which is unable to resolve the class whose name does not match the file's name.
This is true even when importing the _matching_ class beforehand.

For example, the `Foo.groovy` file in `src/com/brendenhyde` contains a package statement of `package com.brendenhyde` followed by two class declarations: `Foo` and `Bar`.
The class `Foo` matches the filename `Foo.groovy` and should therefore be resolved by Groovy at runtime.
The class `Bar` does not match the filename but should still theoretically be resolved, since `com.brendenhyde.Foo` gets imported first in `vars/common.groovy`.
Unfortunately, it doesn't work that way in practice, and the class `com.brendenhyde.Bar` fails to resolve, crashing the pipeline.

### wildcard ✅
The `wildcard` tag shows what happens when two classes are defined in a single `.groovy` file where one of the classes matches the filename (just like the `two-in-one` tag), AND they are imported with a wildcard in `vars/common.groovy` by using `import com.brendenhyde.*` before using both `Foo` and `Bar`.
The result is successful!

<img src="https://github.com/bxbrenden/jenkins-shared-lib-example/blob/main/images/wildcard.gif" width="512" height="256" alt="Animated gif of Charlie Kelly being a wildcard in Always Sunny">

### wildcard-multi-matching ✅
The `wildcard-multi-matching` tag is the same as the `wildcard` tag but with two `.groovy` files instead of one;
the first class file is still `Foo.groovy` and still contains the classes `Foo` and `Bar`, but we now have another file named `Baz.groovy` with a class called `Baz`.

Using the wildcard still imports everything successfully with this config.

### wildcard-non-matching ❌
The `wildcard-non-matching` tag has a single `.groovy` file in `src/com/brendenhyde` whose file name doesn't match its class name;
the file is `Foo.groovy`, and the class defined within is `Bar`.

When `vars/common.groovy` does a wildcard import of `com.brendenhyde.*`, the `Bar` class does not get resolved, resulting in a crashed pipeline.

### non-wildcard-non-matching ❌
The `non-wildcard-non-matching` tag is similar to `wildcard-non-matching` but with an import that doesn't use the `*` character.

Instead, the import in `vars/common.groovy` is done as follows:
```groovy
import com.brendenhyde.Foo
```
This will cause Jenkins to search the Groovy classpath and find the `Foo.groovy` file.
Then, Jenkins sees that there is no `Foo` class in that file and that `Bar` is in there instead.
This crashes the pipeline with the following error:
```
Compilation incomplete: expected to find the class com.brendenhyde.Foo in src/com/brendenhyde/Foo.groovy, but the file contains the classes: com.brendenhyde.Bar
```