Hawt App
========

Makes it easy to create and launch simple flat classpath based main app in java.  It creates a .tar.gz that contains all your app's runtime
dependencies in the lib dir and creates a bin/run script that handles setting up your classpath in the right order and executing your
java with the right arguments if your using a [Maven](http://maven.apache.org) based build

Usage
-------

You can use it on any mvn module which contains a class that can be run from the CLI.  For example if your class containning is `org.apache.camel.cdi.Main`, then 
run:

    mvn package org.jboss.hawt.app:hawt-app-maven-plugin:1.0-SNAPSHOT:build -Dhawt-app.main=org.apache.camel.cdi.Main
    

To create the application archive as part of you default build for the module, add the a plugin confiuration similar to the following to your maven module:

    <plugin>
      <groupId>org.jboss.hawt.app</groupId>
      <artifactId>hawt-app-maven-plugin</artifactId>
      <version>1.0-SNAPSHOT</version>
      <executions>
        <execution>
          <goals>
            <goal>build</goal>
          </goals>
          <phase>package</phase>
        </execution>
      </executions>
      <configuration>
        <main>org.apache.camel.cdi.Main</main>
      </configuration>
    </plugin>

Produced Artifacts
------------------

Using the build goal of this plugin will create a tar.gz archive at: `target/${project.artifactId}-${project.version}-app.tar.gz` and an unpacked version of that at `target/hawt-app`.  If you want to test out launching your app, just
execute the `target/hawt-app/bin/run` script.

Plugin Configuration Options
----------------------------

The following table contains the configuration properties you can set either in the plugin configuration or via a command line Maven propery to adjust the results of the built application archive.

Name | Maven Property | Description 
-----| -------------- | -----------
assembly | hawt-app.assembly | Directory where the application assembly will be created. **Default:** *${project.build.directory}/hawt-app*
archive | hawt-app.archive | Archive file that will be created. **Default:** *${project.build.directory}/${project.artifactId}-${project.version}-app.tar.gz*
archiveClassifier | hawt-app.archiveClassifier | The GAV classifier that will be assigned to the archive. **Default:** *app*
archivePrefix | hawt-app.archivePrefix | the path prefixed on the files within the archive. **Default:** *${project.artifactId}-${project.version}-app/*
main | hawt-app.main | The main class that will be executed by the launch process.
source | hawt-app.source | If this directory exists, then it's contents are used to augment the contents of the application assembly. For example it could be used to add an etc/defaults script to setup environment variables. **Default:** *${basedir}/src/main/hawt-app*

Env Configuration Options
-------------------------

There are several enviorment variables that can be set before running the `bin\run` script to customize your app's startup.  

Enviorment Variable | Description
------------------- | -----------
JVM_ARGS | Options that will be passed to the JVM.  Use it to set options like the max JVM memory (-Xmx1G).
JVM_DEBUG_ARGS | JVM debug arguments
JVM_DEBUG | If set to true, then enables JVM debug on port 5005
JVM_AGENT | Set this to pass any JVM agent arguments for stuff like profilers
SYSTEM_PROPERTIES | Add your '-D' system properties in this enviorment varible.
MAIN_ARGS | Arguments that will be passed to you application's main method.

Runtime Defaults Configuration
------------------------------

If an `${APP_HOME}/etc/defaults` file exists it will be sourced in by the run script and you can use this to set
all the environment variables if you wish.  You can additonally also modify the values of the following variables if you want to change the defaults.

Enviorment Variable | Description
------------------- | -----------
MAIN | The main class that will be executed.
APP | The name of this app, if supported by your system this will be displayed as the process name
CLASSPATH | The classpath of the java application

