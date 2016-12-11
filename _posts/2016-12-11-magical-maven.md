---
title: Intro to Maven
tagline: Learning Maven
description: Figuring out this magical build system
---

Getting another tool on my belt that I should have had years ago.

# What Is Maven?

Something important to note is that Maven is not just a build system. It is a project management and comprehension tool. It attempts to apply patterns to a project's build infrastructure in order to promote comprehension and productivity. The tool brings to a project visibility, reusability, maintainability, and comprehensibility. 

# Jumping Into It

## Archetypes

**Archetype** is a Maven project templating toolkit; typically defined as *an original pattern or model from which all other things of the same kind are made*. They help authors create project templates and the ability to generate parameterized versions of those templates.  

## Setting Up

Create a project using the following. This will create a directory for the specified `artifactId`, the project folder structure, and a **pom.xml**.

~~~bash
mvn -B archetype:generate\
	-DarchetypeGroupId=org.apache.maven.archetypes \
	-DgroupId=com.ElecRabbit.app \
	-DartifactId=new-app
~~~

# POM Files

POM is short for **Project Object Model** and is the basic unit of work in Maven. It contains every important piece of info about a project and is necessary to understand before continuing much further.

POM is an XML file that contains info about the project and config details used to build the project. When executing any task or goal, Maven looks for the POM and reads it first.

Things often in the POM are:

* Project dependencies
* Plugins or goals to be executed
* Build profiles
* Project version
* Project description
* Developers

## Minimum POM

A POM must have, at least:

* Project Root
* modelVersion - Object model used by the POM. Set to 4.0.0.
* groupId - id of the project's group
* artifactId - id of the project
* version - Version of the artifact under the specified group

May also include:

* name - Display name for the project.
* url - Indicates where the project's site can be found
* description - Basic description of the project.

## Super POM

In Maven, much like in OOP, there is a Super POM from which all POM files inherit configurations not shadowed. Below is an example of the most minimal POM file; for those configs not specified Maven will use the inherited defaults. For example, the default packaging type is *jar*.

~~~xml
<project>
	<modelVersion>4.0.0</modelVersion>
	<gropuId>com.ElecRabbit.app</gropuId>
	<artifactId>new-app</artifactId>
	<version>1</version>
</project
~~~

The project's fully qualified artifact name is in the form `<groupId>:<artifactId>:<version>`, so for this it'd be `com.ElecRabbit.app:new-app:1`.


## Dependencies

To add a dependency you need to define it within the `<dependencies>` section of the POM file. Each dependency must have a **groupId**, **artifactId**, **version**, and **scope**.

If the defined dependency is not local, Maven will look to a remote repository to download it.

To know what groupId, artifactId, and version for a dependency to use you can Google it or browse ibiblio for it's *maven-metadata.xml* file.


## Distribution

To deploy a jar to an external repository you must configure the repo url in the *pom.xml* and authentication information for connecting in *settings.xml*.

~~~xml
<distributionManagement>
	<repository>
		<id>my-repository</id>
		<name>My Repository</name>
		<url>scp://repo.elecrabbit.com/repo/</url>
	</repository>
</distributionManagement>
~~~

~~~xml
<servers>
	<server>
		<id>my-repository</id>
		<username>erabbit</username>
		<privateKey>/path/to</privateKey> (default ~/.ssh/id_dsa)
		<passphrase>my_key_pass</passphrase>
	</server>
</servers>
~~~


# Finishing Up

I'll be using Maven to build my projects from now on and regularly refer to the documentation for help. Hopefully over time I'll get a better hang of it. For now, it's good to get the basics down and the real learning will come from application. I may also get a [book](http://maven.apache.org/articles.html) or something to have on hand.


-------------------

# References

* [Introduction to Archetypes](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)
* [Introduction to the POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)
* [Maven Getting Started](http://maven.apache.org/guides/getting-started/index.html)
* [Maven Books and Resources](http://maven.apache.org/articles.html)