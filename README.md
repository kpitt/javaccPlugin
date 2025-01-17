# JavaCC Compiler Plugin for Gradle

[![Build Status](https://travis-ci.org/johnmartel/javaccPlugin.svg)](https://travis-ci.org/johnmartel/javaccPlugin)

:warning: **This project is looking for a new maintainer**
It has been long overdue that I look at Pull Requests, migrate the build system to Gradle 6, make bug fixes, etc. Unfortunately, I have moved to other projects since first introducing this plugin, am no longer using JavaCC on a regular basis and can't anymore spend the time maintaining the plugin the users are entitled to expect. Given all this, I am ready to hand the repository to a new maintainer that is ready to give it the love it deserves. Simply contact me in PM and we'll work it out.

Provides the ability to use [JavaCC](http://javacc.java.net/) via [Gradle](http://www.gradle.org/). If the 'java' plugin is also applied, JavaCompile tasks will depend upon the compileJavacc task.

## Installation

Simply grab the plugin from Maven Central:

Add the following lines to your `build.gradle` script:

Gradle 2.1+
```gradle
plugins {
  id "ca.coglinc.javacc" version "2.4.0"
}
```

Gradle <2.1
```gradle
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath group: 'ca.coglinc', name: 'javacc-gradle-plugin', version: '2.4.0'
    }
}
apply plugin: 'ca.coglinc.javacc'
```

## Building

To build, simply run the following command in the directory where you checked out the plugin source:

`./gradlew clean build`

## Usage

Place your JavaCC code into `src/main/javacc`.
The generated Java code will be  put under `./build/generated/javacc` and will be compiled as part of the Java compile.

Place you JJTree code into `src/main/jjtree`
The generated Java code will be  put under `./build/generated/jjtree` and will be compiled as part of the JavaCC compile.

You can configure the input/output directory by configuring the compileJavacc task:
```gradle
compileJavacc {
    inputDirectory = file('src/main/javacc')
    outputDirectory = file(project.buildDir.absolutePath + '/generated/javacc')
}
```
Due to the nature of the JavaCC compiler and its use of the OUTPUT_DIRECTORY parameter, you should prefer using `inputDirectory` over `source` provided by SourceTask. To use include/exclude filters, please refer to the [SourceTask](http://www.gradle.org/docs/current/dsl/org.gradle.api.tasks.SourceTask.html) documentation. By default, only `*.jj` files are included.

You can configure commandline arguments passed to JavaCC by specifying `javaccArguments` map in compileJavacc:
```gradle
compileJavacc {
    arguments = [grammar_encoding: 'UTF-8', static: 'false']
}
```

Input/output directories and arguments can be configured as well for JJTree:
```gradle
compileJjtree {
    inputDirectory = file('src/main/jjtree')
    outputDirectory = file(project.buildDir.absolutePath + '/generated/jjtree')
    arguments = [grammar_encoding: 'UTF-8', static: 'false']
}
```

### Eclipse

If you are using Eclipse and would like your gradle project to compile nicely in eclipse and have the generated code in the build path, you can simply add the generated path to the main sourceSet and add a dependency on `compileJavacc` to `eclipseClasspath`.
```gradle
sourceSets {
    main {
        java {
            srcDir compileJavacc.outputDirectory
        }
    }
}

eclipseClasspath.dependsOn("compileJavacc")
```

### Dependency on another version of JavaCC

If for some reason you need to depend on a different version of JavaCC than the plugin's default, you can use the following in your build script:
```gradle
dependencies {
    javacc 'net.java.dev.javacc:javacc:[version]'
}
```

### Custom AST classes

To use your own custom AST classes, simply place them in your JavaCC input directory besides your regular .jj files. Make sure to include .java files:
```
compileJavacc {
    include '**/*.java'
}
```

If you prefer, you can simply have them sit in your regular java source set. Of course, a custom AST class needs to be in the same package as the original AST class it overrides.

### JJDoc

The `jjdoc` task looks for JavaCC parser specifications in the default `src/main/javacc` directory and generates documentation into the default `./build/generated/jjdoc` directory.
Of course, you can provide arguments and configure the input/output directories as with the `compileJavacc` and `compileJjtree` tasks:
```gradle
jjdoc {
    inputDirectory = file('src/main/javacc')
    outputDirectory = file(project.buildDir.absolutePath + '/generated/jjdoc')
    arguments = [text: 'true']
}
```

## Compatibility

This plugin requires Java 6+.

It has been tested with Gradle 1.11+. Please let us know if you have had success with other versions of Gradle.

## Signature

The artifacts for this plugin are signed using the [PGP key](http://pgp.mit.edu:11371/pks/lookup?op=get&search=0x321163AE83A4068A) for `jonathan.martel@coglinc.ca`.

## Releasing

The following command can be used to release the project, upload to Maven Central and upload to Bintray:
```./gradlew -PreleaseVersion=[version] -PnextVersion=[snapshot version] -PscmUrl=https://github.com/javacc/javaccPlugin.git -PossrhUsername=[username] -PossrhPassword=[password] -PgpgPassphrase=[passphrase] -PbintrayUser=[username] -PbintrayApiKey=[apiKey] clean :release:release```

## Changelog

### 2.4.0
- Allow configuration of the JavaCC version to use (Issue #29)
- Improve incremental build support by adding [@InputDirectory](https://docs.gradle.org/current/javadoc/org/gradle/api/tasks/InputDirectory.html) to the tasks input property
- Build with Gradle 3.2.1
- Upgraded dependencies to latest
- Run acceptance tests with Gradle TestKit

### 2.3.1
- Fix handling of generated files when rerunning tasks (Issue #20)

### 2.3.0
- Added support for JJDoc

### 2.2.2
- Handle custom AST classes correctly in the compileJjtree task (Issue #16)

### 2.2.1
- Fixed support for custom AST classes defined in the Java sourcesets (Issue #15)

### 2.2.0
- Added support for custom AST classes (Issue #13)
- Plugin now builds with Gradle 2.5
- Upgraded plugin dependencies for bintray and coveralls
- Upgraded dependencies to latest

### 2.1.0
- Added support for JJTree: compileJjtree task is now added by default and compileJavacc depends on it (Issue #4, Pull Request #9)

### 2.0.4
- Plugin now builds with Gradle 2.2.1
- Now publishes to Bintray using the latest version of com.jfrog.bintray plugin and syncs to Maven Central via this plugin

### 2.0.3
- CompileJavaccTask is now a [SourceTask](http://www.gradle.org/docs/current/dsl/org.gradle.api.tasks.SourceTask.html) and supports include/exclude filters
- Can now configure the input/output directory

### 2.0.2

- Improved the build system
- Added acceptance tests
- Support the gradle wrapper (Issue #10)
- Support passing optional arguments to Javacc (Issue #11)
- Support multiproject builds (Issue #3)

### 2.0.1

Fixed the gradle-plugin attribute for the Bintray package version.

### 2.0.0

- Migrated to Gradle 2.0
- Plugin id changed to 'ca.coglinc.javacc'
- Plugin is now available via the [Gradle Plugins repository](http://plugins.gradle.org)

### 1.0.1

Updated JavaCC to 6.1.2.

### 1.0.0

Initial version with limited features. Simply generates JavaCC files to Java from a non-configurable location into a non-configurable location.
