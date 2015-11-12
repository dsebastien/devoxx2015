# Advanced Java Modularization

## Migration approach
top-down vs bottom up

Typical application:
* application jars
* libraries jars
* jdk

Top down:
* what does the module require?
* what does the module export?

`jdeps -s lib/myapp.jar lib/mylib.jar: analyze modules usage


## Automatic modules
* "real" modules
* no changes to someone else's JAR file
* ...

?

## Library migration
Migration from the bottom
* what does the module require?
  * check using jdeps
* what does the module export?

`jdeps -genmoduleinfo src *.jar`: create module-info.java files easily

`jar --create --file mlib/jackson.core-2.6.3.jar --module-version 2.6.3 -C mods/jackson.core .`: create a modular jar file. Specifies also the version (metadata not part of the module-info.java file)

`java -mp mlib --cp lib/myapp.jar:lib/mylib.jar -addmods jackson.databind myapp.Main`: ...


```
package java.lang.reflect;

public final class Module {
    public String getName();

    public boolean canRead(Module source);
    public Module addReads(Module source);
    ...
}
```

To configure access for an additional module:
```
Module source = clazz.getModule();
this.getClass().getModule().addReads(source);
```

The above indicates that the current module needs to read the module of the given source.

