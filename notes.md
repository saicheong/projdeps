# Leiningen and Maven
This is about Leiningen and in particular how it uses Maven/Maven infrastructure.

## Error synchronising <project name>: Failed to collect dependencies for <library>
IntelliJ/Cursive Problem?

Googling cursive dependency resolution error:
https://github.com/cursive-ide/cursive/issues/1930

https://groups.google.com/forum/#!topic/cursive/ICuj7MuTF28
lein version 2.7.1 - will use http whereas lein 2.8.1 uses https only
The lein version that Cursive is a project level setting in IntelliJ: 
Settings -> Build, Execution, Deployment -> Build Tools -> Leiningen -> Project Settings

https://clojurians-log.clojureverse.org/cursive/2016-12-21
Network issues

Would making all dependencies available in the local repo resolve the network issues? Or
at least stop Cursive/IntelliJ from complaining? 
See Notes about Maven Repositories below.

## Notes About Maven Repositories

Local and remote repositories are structured the same way so that scripts can run on either 
side, or they can be *synced for offline use*. The layout of the repositories is completely 
transparent to the Maven user, however.

By default, Maven will download from the central repository.
To override this, you need to specify a mirror. This can be set in settings.xml (global) or
in the project pom.xml

```xml
<settings>
  ...
  <mirrors>
    <mirror>
      <id>UK</id>
      <name>UK Central</name>
      <url>http://uk.maven.org/maven2</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
  ...
</settings>
```

Use a mirror to _replace_ a particular repository. You want to replace a repository for various
reasons:
* one that is geographically closer
* your own internal repository you have more control over
* provide a local cache to a mirror and need to use the url of the mirror

You force Maven to use a single repository by having it mirror all repository requests. 
This is done by setting `mirrorOf` to `*`. 
See [Guide to Mirror Setting](https://maven.apache.org/guides/mini/guide-mirror-settings.html)
for more advance settings.

### Internal Repository

When it is not feasible (eg for security reasons) to connect to internet to download 
dependencies, an internal repository can be set up to house a copy of the dependencies.

To Maven, the internal repository is just another remote repository. To use the internal
repository - just add a `repositories` element:

```xml
<project>
  ...
  <repositories>
    <repository>
      <id>my-internal-site</id>
      <url>http://myserver/repo</url>
    </repository>
  </repositories>
  ...
</project>
```

### Dev with Maven

Maven accepts the offline switch -o.


## Leiningen Maven Settings
Leiningen does not support .m2/settings.xml - simply because Leiningen isn't maven

See: http://grokbase.com/p/gg/clojure/132n4ptwdm/does-lein2-use-repositories-defined-in-m2-settings-xml

So one has to copy the repository configurations in settings.xml into project.clj or put
it into user profiles

See: https://stackoverflow.com/questions/15910567/does-leiningen-read-maven-settings-in-m2-settings-xml


### Leiningen Tricks

Use `:local-repo` options to have all the dependencies stored in one cache. This dependencies can then
be copied/used in a machine behind a proxy.  