# Introduction to Maven

## What is Maven
Use as a **build tool**:
* Build source code and product an artifact (e.g. a component, a JAR or a ZIP file).
* Manage dependencies - resolve dependency versions and download.

Use as a **project management tool**:
* Handles versioning of code and releases.
* Produce Javadocs and site information.

### Uses of Maven
* Repeatable builds - recreate environment by externalising settings. This allows isolation of the code from the build environment, and allows recreation independant of the environment/OS.
* Transitive dependencies - it can resolve dependencies and download these automatically.
* Environment - provides an environment to build.
* Local repository - acts as a local repository for dependencies, so the same dependency can be shared across multiple projects rather than being duplicated.
* Prefered build method by CI tools, such as Jenkins.

## Ant vs Maven
### Ant
* Originally designed as a replacement for Make (UNIX only) to be cross-platform and less-brittle.
* Build with Java and XML-based.
* Quite verbose to use. Requires everything to be explicitly defined by the user (e.g. variables, paths, commands).

### Maven
* Full featured build tool, rather than a scripting tool.
* Implict - everything does not have to be defined to work.
* Consistency.
* Inheritence.
* Transitive dependencies.
* Versioned.

### Pros/Cons
| Maven | Ant| 
|-|-|
| Black box | Easily trace |
| Steeper learning curve | Quick to learn |
| Convention over configuration | Copy and paste |
| Rich IDE integration | Large project size |
| Less overhead | |
| Different mindset | |