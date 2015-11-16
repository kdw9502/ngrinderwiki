**I had run the test with 2 process and 2 threads and I checked the log.The number of HTTP call seems like half of what I expected. What's wrong?**

> The underlying The Grinder engine makes a log file per process in the agent side when it executes a test. So if a test use 2 processes, it will generate 2 log files. These logs a test generate can be very huge(even giga-byte level) depending on the how much the error occurs and how long a test is executed. So we made nGrinder agents limit the size of log file and only passes only one log file per a agent process to controller. 

**Why is record() not supported?**

> nGrinder 3.1 or prior use The Grinder 3.9.1 as the internal engine. The Grinder 3.9.1 let user choose to use Jython 2.2 or Jython 2.5. As default it uses Jython 2.2. record() is for Jython 2.5 and nGrinder does not ship with this version of Jython. We're planning to support Jython 2.5 soon. If you likely to use Jython 2.5 this time, simply download Jython 2.5 jar file from http://search.maven.org/remotecontent?filepath=org/python/jython/2.5.3/jython-2.5.3.jar and locate it the lib folder in the script list. Check How to use library. If you want to use python library embedded in jython, you should upload jython 2.5.3.jar which you can download from http://search.maven.org/remotecontent?filepath=org/python/jython-standalone/2.5.3/jython-standalone-2.5.3.jar.

>**From nGrinder 3.1.1 uses Jython 2.5 as a default jython engine. You don't need to upload jython 2.5 by yourself.**

**Why are image files in the resources folder not transfered to agents?**

> nGrinder 3,1 or prior distribute only files having "xml", "txt", "json", "properties" extension as resources. So even you upload "jpg", "png" in resources folder, they are ignored. We'll make it transfer every file in resources folder in nGrinder 3.1.1. Please wait.

>**From nGrinder 3.1.1, it transfers all files in the resources folder to agents .**

**Why are not my agents connecting the controller even after setting agent.conf?**

> nGrinder uses ${user.home}/.ngrinder folder to store and retrieve data. However if you install 32Bit JVM on 64bit JVM. ${user.home} returns ? then it does not work well. Please see http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=6972329 . The solution is to install 64bit JVM on 64bit OS.

>Or... you may not have configured /etc/hosts well. Please check the Installation Guide again.