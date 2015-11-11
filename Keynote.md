# Keynote

## Java 9

### Modules
Project Jigsaw

* fix the classpath issue
* reduce footprint
* break the monolith
* fix the issue of private vs public api
* prevent the usage of internal APIs
* increase startup performance

module-info.java (the - makes it an invalid class name)
```
module com.foo.bar {
    requires com.foo.baz;
    exports com.foo.bar.alpha;
    exports com.foo.bar.beta
}
```

`javac module-info.java`: creates a module descriptor

A module jar file has a module-info.class file at the root: works with both JDK8 & 9.

All modules implicitly import java.base (contains io, lang, lang annotation, invoke, module, ref, reflect, math & net)

JDK9 loads the module graph and identifies is something is missing, if there is duplication, etc.

Each module can export types, providing accessible types which enable strong encapsulation.

Modules cannot use types that are not exported by other modules.

JMOD files: native modules

jlink:
* provides linking: takes in modular jar files (and jmod files) and creates images that only contain the required code

The JRE has been reorganized.

Internal APIs are going to be concealed:
* sun.*
* *.internal.*

jdeps:
* static analysis tool for jar files
* analyze the usage of JDK internals
  * jdeps -jdkinternals app.jar
    * helps preparing for JDK9
