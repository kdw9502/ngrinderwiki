* This document is subject to be changed for ngrinder 3.4

>JetBrain IntelliJ IDEA is the most excellant IDE in the Java world and a lot of open source developers are adopting it in their daily life tools.

IntelliJ is the quite good match to nGrinder Groovy Maven project because it already includes all the plugins(Groovy, SVN, Maven) in its default package and needs very few configurations to run nGrinder test cases. Please follow the below descriptions.

1. Install IntelliJ IDEA Community Edition (It’s free)
    - http://www.jetbrains.com/idea/download/
2. Run IntelliJ IDEA. Enables SVN(Select VCS Integration Plugins page), Maven, Groovy supports(Slect Other Plugins Page).
3. Select “Check out from Version Control”  
   ![](assets/Import-Groovy-Maven-Project-in-IntelliJ-0a75b.png)
4. Select the Subversion as VCS and type the SVN url in which SVN maven project is stored. Then type ID / PW.
5. Then check out.
6. In the imported Project, select the “Add Framework Support”  
   ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_2.png)
7. Select Groovy and Click OK.  
   ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_3.png)
8. Open Maven View.  
   ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_4.png)
9. Select the test groovy file and Select “Run Test” on the context menu.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_6.png)
10. Then you will see the following output on the Run panel. Please copy –javaagent… part  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_7.png)
11. Click Run=>Edit Configuration Menu.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_9.png)
12. Change the default JUnit VM options to have the javaagent option.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_10.png)
13. Delete JUnit>TestRunner.test configuration.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_11.png)
14. Then run the step 10 again. You will see the following test output view.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_12.png)
15. Debug test cases and commit the code into SVN when you finished.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_13.png)
16. Then run this script in nGrinder!! That’s it!!
