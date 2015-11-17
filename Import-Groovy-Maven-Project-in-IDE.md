>Groovy Maven Project is structured for users to easily import the project into IDE. If you already have an IDE which supports Groovy and Maven, you can easily import it. However if you don’t have it yet, please follow the instruction in [Install Groovy IDE](install-groovy-ide) first. If you prefer to use IntellJ instead of Eclipse, go [Import Groovy Maven Project in IntelliJ](import-groovy-maven-project-in-intellij).

If you have installed the Eclipse based Groovy IDE and created Groovy Maven Project in nGrinder subversion, you can import the Groovy Maven Project using the subversion client in Eclipse.

![](http://www.cubrid.org/files/attach/images/379199/570/652/image_2.png)

Notice that there is the subversion URL field in the script management page in nGrinder. The above screenshot shows the our test instance. In this instance, the created Groovy Maven project can be accessed through http://localhost:8080/svn/admin/project URL according to the above page.  
OK. Let’s import the project.

1. Open your Eclipse and Choose File ==> Others ==> Maven ==> Check out Maven Projects from SCM  
   ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_1.png)
2. Select svn in the SVN URL and type the SVN URL provided in nGrinder. Then click Finish.  
   ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_6.png)
3. Then the project will be imported.  
   ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_8.png)
4. Enable Groovy on this project.  
   ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_4.png)
5. Remove groovy libraries from classpath by selecting "Groovy ==> Remove Groovy libraries from classpath".  
   It's because there is a conflict between groovy libraries provided by Maven and Eclipse.  
   ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_8.png)
6. Select the Groovy test case and Select Run As ==> JUnit Test.  
   ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_5.png)
7. Then you might see the following error. It’s because the specialized nGrinder JUnit Runner(named GrinderRunner) needs a special JVM argument for instrumentation  
   ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_6.png)
8. Check the error message and add the JVM argument following the instruction. Select Run As ==> Run Configurations ==> Select Test1 ==> Select Arguments panel.  
   ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_7.png)
   Please be aware that the argument varies depending on the user account. So provide the exact argument which is shown in the JUnit error message. This setting should be provided for every JUnit test running set. If you like to make it as a default vm option for every test run. You can set it up in the global configuration. Please check [Install Groovy IDE](install-groovy-ide) for details.
9. Then run as JUnit test again.  
   ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_9.png)
10. In my case, the access to please_modify_this.com causes the error. Ater changing this to http://www.google.com . It shows the green bar now.  
    ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_10.png)
11. You can add the break point in the test case. Then select Debug As ==> JUnit Test.  
    ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_11.png)
12. Then you can debug the test case using the generic Eclipse debugging feature.  
    ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_13.png)
13. When you think the test case writing is almost completed, you may want to see the result in the repeated run situation. You can provide the @Repeat annotation on the test case class for this need. Then run as JUnit test again.  
    ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_15.png)
14. Then you can see that test case is executed multiple times you specified. Please note that it’s 100 repetition by single thread. (not multi thread or multi process)  
    ![](http://www.cubrid.org/files/attach/images/379199/570/652/image_thumb_16.png)
15. If you think the test case is ready, commit it into subversion selecting Team ==> Commit. Finally run this test in nGrinder.