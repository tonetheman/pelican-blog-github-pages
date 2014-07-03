Title: getting started with gradle and java
Date: 2014-07-02 21:53
Tags: java,gradle
Category: gradle
Slug: getting-started-with-gradle-and-java
Author: tonetheman@gmail.com
Summary: how to get started with gradle and java


# Using Gradle with Java

First off for the moment ignore the manual. While it is a fine manual it is useless for what I think is a fairly standard set of use cases.

## Directory setup
---------------
If you are old school like me I had a src directory at the top of my project. That might work with gradle but gradle has some serious thoughts about how you set up a project and putting a src directory at the top of your project is not one of them.

To make your life easier and because Java programmer freaking love to nest crap for no apparent reason, you will have the following set for your directories.
```
src/main/java
```

That means if you have no packages in your source all of your Java files will be in the above mentioned directory.

If you have a package structure like com.foo then your directory setup will look like this
```
src/main/java/com/foo/<SOURCE FILES HERE>
```
While I can see some goodness in structuring things like this, ultimately it ends up being lots more directory nesting with no payoff for small projects.



## I need to compile some Java files and make a jar.
-------------------------------------------------
Assuming you have the directory set like I mentioned earlier, then this will be your build.gradle file.
```
apply plugin: 'java'
```
Yup that is it. I am not making that up. Super simple. Type the following line at the top of your project.
```
gradle jar
```
When the computer is done killing some cats look in the build/lib directory for you nice happy jar of class files.

This is where gradle shines. Insanely small amounts of code can do a lot of work. At the cost of some configuration of directories you get a lot of stuff for free.

## I need to compile some Java files along with a 3rd party jar I am using that I just downloaded.
-----------------------------------------------------------------
This is almost always my default case. I do this particular thing a lot. I mean a lot. So it infuriates me that this is not covered in the fine gradle manual.

The main change here is that there is now going to be a lib directory at the top level of your project. And you will place all of the jars that are needed for your project in that directory.

Like this
```
./lib/json.jar
./src/main/java/com/foo/MainlineUsesJSON.java
```
So in this case what does the gradle file look like?

```
apply plugin: 'java'

dependencies {
	compile fileTree("lib")
}
```

Once again that is it. Type the following line to compile your project
```
gradle compileJava
```
What makes this work is that the line compile fileTree bit tells gradle that when you compile something there are some dependencies in the lib directories, use all of them.

This will get you started using gradle and java.

