This document discusses how to set up an nGrinder project in Eclipse.

First, we recommend developers adopt to Spring Tool Suite (STS) as their main development platform.

Because STS is conveniently integrated with Git and Maven, enabling developers to focus on development rather than getting entangled with configuration-related issues.

Currently, we use STS version 2.9.2.

### 1. Clone the nGrinder Project from Github
change perspective to “Git Repository Exploring”

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_3.png)

and then we will clone a Git repository.
Just input this in the URI field:
```
https://github.com/nhnopensource/ngrinder.git
```

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_5.png)

and click “Next.”

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_6.png)

Please specify nGrinder Project path on your local drive and click “Finish” and wait for a while.

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_7.png)

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_8.png)

nGrinder Project is already in our local drive!

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_8.png)

### 2. Importing nGrinder Project
The instructions above describe how to clone an nGrinder project from Github to our local drive.

As follows, we will import the project into our eclipse workspace.

First, return from “Git Repository Exploring” perspective to “Java” perspective,

and import the nGrinder project from the local Git Repository

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_10.png)

please select “Local” and click “Next”

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_11.png)

select “Import as general project” and click “Finish”

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_12.png)

### 3. Use Maven to build an nGrinder Project
Now that the nGrinder Project can be seen in “Java perspective”.

we should import modules separately as Maven Projects.

because an nGrinder Project consist of three modules ngrinder-controller,ngrinder-core,and grinder-dns.

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_13.png)

Select Root Directory and click “Next”, nGrinder sub Projects will be imported automatically.

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_14.png)

It’s time to build an nGrinder project.

First, open “Run Configurations” and config new “Maven Build.”

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_15.png)

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_17.png)

Compiling of the project is successful !

Let’s deploy the nGrinder package .

An additional tomcat server should be used ,because it’s faster than the STS self-install Server.

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_18.png)

Our tomcat server is finished !

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_19.png)

Ok, let`s check it from the browser.

![](http://www.cubrid.org/files/attach/images/379199/793/475/image_thumb_20.png)