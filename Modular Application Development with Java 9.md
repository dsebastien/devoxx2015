# Modular Application Development with Java 9

## Links
* early access builds: https://jdk9.java.net/jigsaw/

## Implied readability

```
module java.sql {
    requires public java.logging; // implied readability
}
```

## Commands
* java -listmods: lists all standard modules
* java -modulepath dir1:dir2:dir3...
  * try to resolve in dir1, if not found search in dir2, ... if found in dir2 and present in another one then those will be ignored. Duplicates are not permitten in a dir
* java -modulpaths mods -m com.foo.app/com.foo.ap.Main
* java -Xdiag:resolver -mp mods -m com.foo.app/com.foo.a.Main
  * get details about the module loading
* jar --create --file mlib/app.jar --main-class com.foo.a.Main -C mods/com.foo.a .
* jar --file mlib/app.jar -p or --print-module-descriptor
  * display the module descriptor

## Linking
* jlink --moduleath $JDKMODS:$MYMODS --addmods com.foo.ap --output myimage

## System module path
...

## Versioning and dependencies
Not supported!

Up to build tools to arrange modules on the module paths to arrange that correct modules are available.
