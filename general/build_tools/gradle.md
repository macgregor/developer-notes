# Gradle
Gradle is a build system, similar to Maven or Ant, which can be used to manage project dependencies, build and test your application. It supports Maven and Ivy repositories allowing you to reuse existing infrastructure.

Gradle is commonly used to build android projects, so many have exposure to it from that world. It is also popular because it doesn't use xml, unlike maven or ant. Instead it has its own DSL which is generally more terse and intuitive. It tends to have less boiler plate than its cousins as well.

Another extremely powerful feature in Gradle is that it allows you to easily extend functionality within your build.gradle file by writing Groovy or Kotlin code whenever you need it. You can extend Ant and Maven functionality but it is much more difficult.


## build.gradle

```gradle
apply plugin: 'java'
apply plugin: 'checkstyle'
apply plugin: 'findbugs'
apply plugin: 'pmd'

version = '1.0'

repositories {
    mavenCentral()
}

dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.11'
    testCompile group: 'org.hamcrest', name: 'hamcrest-all', version: '1.3'
}
```

This is the file used to specify how to build your project, it is where you will spend most of your time fiddling with Gradle bits. It is analogous to pom.xml in the maven world.


## Dependency Management
```gradle
dependencies {
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    testCompile 'junit:junit:4.12'
}
```
Specifying dependencies allows Gradle to manage the classpath of your project, adding jar files from the local filesystem or downloading them from a remote repository. A Java library is identified by Gradle via its project’s `groupId:artifactId:version` (also known as GAV in Maven). This GAV uniquely identifies a library in a certain version.

```gradle
dependencies {
    //verbose dependency
    compile group: 'commons-collections', name: 'commons-collections', version: '3.2'
    testCompile group: 'junit', name: 'junit', version: '4.+'

    //terse Dependency
    compile "io.dropwizard:dropwizard-core:1.2.3"

    //multiple dependencies
    testCompile(
            "io.dropwizard:dropwizard-testing:1.2.3",
            "org.assertj:assertj-core:3.9.0"
    )

    //specify specific files from the local file system
    runtime files('libs/library1.jar', 'libs/library2.jar')

    //including all jars in a local directory
    runtime fileTree(dir: 'libs', include: '*.jar')
    compile fileTree(dir: "${System.properties['user.home']}/libs/cargo", include: '*.jar')

    //exclude transitive dependencies
    compile 'org.springframework:spring-web:4.3.10.RELEASE' {
        exclude group: 'com.google.code.gson', module: 'gson'
    }
}
```


### Repositories
```gradle
repositories {
    mavenCentral()
}
```
You can specify remote repositories to look for dependencies in. Gradle supports Maven and Ivy repositories among others (such as JCenter).


There are a few ways to specify repositories:
```gradle
repositories {
    //Maven central is a first class citizen
    mavenCentral()

    //custom maven repo from a url
    maven {
        url "http://repo.mycompany.com/maven2"
    }
    maven ("http://jcenter.bintray.com/")

    //ivy is a thing
    ivy {
        url "http://repo.mycompany.com/repo"
    }

    //so is jcenter
    jcenter {
        url "http://jcenter.bintray.com/"
    }
}
```


## Tasks
```gradle
task hello {
    doLast {
        println 'Hello Gradle'
    }
}
```

Each Gradle project consists of tasks. A task represents a piece of work which a build performs, e.g., compile the source code or generate the Javadoc. It is a [closure](http://groovy-lang.org/closures.html) int he Groovy programming language, which is a whole topic I wont get in to for now you can think of it as a functional object. Tasks can be configured to run as part of the Gradle lifecycle automatically and declare dependencies to enforce order though in my limited experience can be much harder than you would expect (I gave up trying to figure out how to copy a generated file to a new location at the end of a build). Tasks can also be executed explicitly, the above task could be triggered by calling `gradle hello`.


Tasks will be executed in different phases depending on how they are defined.


### Gradle Lifecycle
Gradle lifecycle consist of three phases: initialization, configuration, and execution.

1. **Initialization** - Gradle decides which projects are to participate in the build.
2. **Configuration** - task objects are assembled into an internal object model, usually called the DAG (for directed acyclic graph). It can be used to setup variables or data structures needed by a task action. The code specified directly in a task’s closure is executed during the configuration phase. The configuration block is executed for every available task and not only for those tasks which are later actually executed.
3. **Execution** - build tasks are executed in the order required by their dependency relationships. Tasks specified with a `doLast` or `doFirst` will be executed here.


#### Lifecycle Demo
```gradle
task config1 {
  println 'Config block 1'
}

task config2 {
  println 'Config block 2'
  doLast{
    println 'Execution block 1'
  }
}

task config3 {
  println 'Config clock 3'
  doLast{
    println 'Execution block 2'
  }
}
```

Running `gradle config1` shows all three config block print lines are triggered:
```
> gradle config1

Configure project :
Config block 1
Config block 2
Config clock 3


BUILD SUCCESSFUL in 0s
```

The neither of the execution block texts are printed because there are no build tasks to execute. If we explicitly run the `config3` task we will see all three config block print lines are displayed as well as the execution block of the task we ran:

```
> gradle config3

Configure project :
Config block 1
Config block 2
Config clock 3

Task :config3
Execution block 2


BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
```

### Task Dependencies
Tasks can declare dependencies on each other to ensure other tasks are run and to try to enforce a modicum of execution order

We can specify that config3 depends on config2 and it will be executed when we call `gradle config3`:
```gradle
task config1 {
  println 'Config block 1'
}

task config2 {
  println 'Config block 2'
  doLast{
    println 'Execution block 1'
  }
}

task config3(dependsOn: 'config2') {
  println 'Config clock 3'
  doLast{
    println 'Execution block 2'
  }
}
```

```
> gradle config3

Configure project :
Config block 1
Config block 2
Config clock 3

Task :config2
Execution block 1

Task :config3
Execution block 2


BUILD SUCCESSFUL in 0s
2 actionable tasks: 2 executed
```


## Plugins
```gradle
apply plugin: 'java'
```

PLugins extend core Gradle functionality, typically adding some preconfigured tasks you can make use of. A common plugin is the Java plugin. The plugin adds tasks for compiling, running and unit testing Java code to name a few. A plugin is included in the build.gradle file with the `apply plugin: 'pluginname'` statement (legacy) or specifying the id of the plugin in the `plugins` closure (suggested). Gradle provides also a registry for plug-ins via [Gradle Plugin search](https://plugins.gradle.org/).

```gradle
//legacy plugin
apply plugin: 'java'

//suggested method
plugins {
    id 'java-gradle-plugin'
    id 'com.jfrog.bintray' version '0.4.1'
}

//applying a script plugin, can be a url or a local filesystem resource
apply from: 'https://raw.github.com/akhikhl/gradle-onejar/master/pluginScripts/gradle-onejar.plugin'
```

## How Do I...

### make comments
```gradle
// Single line comment

/*
 Multi
 line
 comment
*/
```

### reference variables in a string
```gradle
dropwizardVersion = '1.2.3'
compile (
            "io.dropwizard:dropwizard-core:${dropwizardVersion}"
)
```
Note the double quotes, single quotes will not properly resolve the variable.


### use gradle with intellij
[Ill let them pontificate on that](https://www.jetbrains.com/help/idea/gradle.html). Its pretty straight forward for the most part though, you just need to create a new gradle project or import an existing one by navigating to your build.gradle file. The rest should be automagic.

### .gitignore all these files
```
# Android built artifacts
*.apk
*.ap_
*.dex

# Java build artifacts class files
*.class

# other generated files
bin/
gen/
build/

# local configuration file (for Android sdk path, etc)
local.properties

# OSX files
.DS_Store

# Eclipse project files
.classpath
.project

# Android Studio
*.iml
.idea
.gradle


#NDK
obj/
```

### access system variables like the user's home directory
```gradle
def homePath = System.properties['user.home']
```


### create/initialize a new gradle project
```
> gradle -q help --task init
Detailed task information for init

Path
     :init

Type
     InitBuild (org.gradle.buildinit.tasks.InitBuild)

Options
     --type     Set type of build to create.
                Available values are:
                     basic
                     groovy-application
                     groovy-library
                     java-application
                     java-library
                     pom
                     scala-library

     --test-framework     Set alternative test framework to be used.
                          Available values are:
                               spock
                               testng

Description
     Initializes a new Gradle build.

Group
     Build Setup
> gradle -q init --type basic
```


## Quirks

### gradlewrapper, gradlew, gradlew.bat
A common and suggested way of using Gradle with your project is to actually package gradle with your source code so that developers can get up and running with your code quickly. They don't even need to have Gradle installed before hand. The [Gradle Wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html) is a script that invokes a declared version of Gradle, downloading it beforehand if necessary. Simply clone a repository and run `./gradlew build`.


### whats up with these << I see on tasks sometimes
Its a [vestige](http://mrhaki.blogspot.com/2016/11/gradle-goodness-replacing-operator-for.html) of an older version of gradle and has been replaced with `doLast`. You should get deprecation warnings when you run the build for the project.


## Conclusion
Gradle is a pretty cool build tool. The learning curve isn't too steep, but the DSL can look a little strange at first. There also seems to be multiple ways to do a single thing so if you aren't careful that could lead to some mess build.gradle files. Overall I like the terse DSL and the flexibility being able to inject logic into your build via Groovy. The extra files and directories in your source repository (gradle/, gradlew, gradlew.bat, build/) can be a bit annoying, especially when combined with other tools that require littering your repository with configuration files (Travis CI, Heroku, Docker, gitbooks...) can start to add up but the power they provide is worth it.

### Additional Resources
* [The Gradle build system: Tutorial - vogella.com](http://www.vogella.com/tutorials/Gradle/article.html)
* [Java Build Tools: Ant vs Maven vs Gradle - technologyconversations.com](https://technologyconversations.com/2014/06/18/build-tools/)
* [The Gradle Wrapper - docs.gradle.org](https://docs.gradle.org/current/userguide/gradle_wrapper.html)
* [Using Gradle Plugins - docs.gradle.org](https://docs.gradle.org/current/userguide/plugins.html)
* [Gradle Goodness: Replacing << Operator For Tasks - mrhaki.blogspot.com ](http://mrhaki.blogspot.com/2016/11/gradle-goodness-replacing-operator-for.html)
* [Introduction to Gradle Lifecycle and Tasks - javajee.com ](http://javajee.com/introduction-to-gradle-lifecycle-and-tasks)
