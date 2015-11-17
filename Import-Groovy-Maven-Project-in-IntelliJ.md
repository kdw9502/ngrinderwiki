>JetBrain IntelliJ IDEA is the most excellant IDE in the Java world and a lot of open source developers are adopting it in their daily life tools. Recently nGrinder dev team switched the IDE from Eclipse to IntelliJ as well.

IntelliJ is the quite good match to nGrinder Groovy Maven project because it already includes all the plugins(Groovy, SVN, Maven) in its default package and needs very few configurations to run nGrinder test cases. Please follow the below descriptions.

1. Install IntelliJ IDEA Community Edition (It’s free)
    - http://www.jetbrains.com/idea/download/
2. Run IntelliJ IDEA. Enables SVN(Select VCS Integration Plugins page), Maven, Groovy supports(Slect Other Plugins Page).
3. Select “Check out from Version Control”  
   ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_1.png)
4. Select the Subversion as VCS and type the SVN url in which SVN maven project is stored. Then type ID / PW.
5. Then check out.
6. In the imported Project, select the “Add Framework Support”  
   ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_2.png)
7. Select Groovy and Click OK.  
   ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_3.png)
8. You need to modify the pom.xml to make the test runnable in IDE. Open pom.xml and add followings.  
   ```
<profiles>

        <profile>

            <id>intellij</id>

            <dependencies>

                <!-- the dependency to ngrinder-groovy will not be scoped as

                    provided when the intellij profile is activiated -->

                <dependency>

                        <groupId>org.ngrinder</groupId>

                        <artifactId>ngrinder-groovy</artifactId>

                        <version>3.3</version> <!-- provide the appropriate version here -->

                </dependency>

            </dependencies>

        </profile>

</profiles>
```
9. Open Maven View.  
   ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_4.png)
10. Activate IntelliJ profile and Click “Reimport Maven Project…” on the left top of the below view.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_5.png)
11. Select the test groovy file and Select “Run Test” on the context menu.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_6.png)
12. Then you will see the following output on the Run panel. Please copy –javaagent… part  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_7.png)
13. Click Run=>Edit Configuration Menu.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_9.png)
14. Change the default JUnit VM options to have the javaagent option.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_10.png)
15. Delete JUnit>TestRunner.test configuration.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_11.png)
16. Then run the step 11 again. You will see the following test output view.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_12.png)
17. Debug test cases and commit the code into SVN when you finished.  
    ![](http://www.cubrid.org/files/attach/images/379199/941/804/image_thumb_13.png)
18. Then run this script in nGrinder!! That’s it!! 