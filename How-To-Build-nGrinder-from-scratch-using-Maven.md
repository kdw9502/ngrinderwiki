This document discusses how Maven can be used to build nGrinder. For the sake of simplicity, we are assuming this entire Configuration will be operated on Linux. First, JDK (version 6) or later, Git 1.7.8 and Maven 3.0.4 will be installed.

### 1. Clone nGrinder Project from Github
just use the command to clone the nGrinder project from Github
```
git clone https://github.com/nhnopensource/ngrinder.git
```

```
$ git clone https://github.com/nhnopensource/ngrinder.git
Cloning into 'ngrinder'...
remote: Counting objects: 26998,done.
remote: Compressing objects: 100% (10897/10897),done.
Receiving objects: 1% (364/26998), 76.00 KiB | 10 KiB/s
```

It may takes several minutes to do this.
```
$ ls ngrinder/
checkstyle.xml docs ngrinder-core ngrinder-dns ngrinder-controller
README.md pom.xml release_note.txt
```

Look ! nGrinder codes already in Local !

### 2. Build nGrinder
Let`s start using Maven to build an nGrinder project. It is quite simple ! 
Just execute the following command in the ngrinder folder.

```
$mvn compile -Dmaven.test.skip=true
```

**-Dmaven.test.skip=true  Use this to skip the test case in order to save time.**

if want to generate war and jar files ,please use **"package"** instead of **"compile"**.

![](http://www.cubrid.org/files/attach/images/379199/168/474/2_thumb.png)

Building nGrinder is complete!