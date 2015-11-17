> When you create Groovy Maven project, you can use IDE to write and debug test cases. Groovy maven project use maven’s pom.xml to structure src folders and specify dependencies. Therefore if your IDE supports Groovy and Maven project, you can use it. For who don’t have IDE yet. This explains how to install Eclipse based Groovy Maven IDE.  
If you prefer to use IntellJ instead of Eclipse, You can skip this instruction because IntelliJ is shipped with the necessary plugins. All you need to do is how to [Import Groovy Maven Project in IntelliJ](import-groovy-maven-project-in-intellij).

1. Download and unzip the lastest “Eclipse IDE for Java Developers” which fits to your OS.
    - http://www.eclipse.org/downloads/packages/eclipse-ide-java-developers/junosr2
    - The above version already includes Maven Eclipse plugin but Groovy and Subversion. You need to separately install them. 
    - Firstly, run the downloaded eclipse.
2. Install Groovy
    1. Go to “Help” ==> “Eclipse Market Place”
    2. Type "groovy" in the search field. Then click the install button in Groovy Eclipse for Juno. (Aha! It's my name Juno :-) Kidding)  
    ![](http://www.cubrid.org/files/attach/images/379199/127/651/image_thumb_4.png)  
    3. Then install all.  
    ![](http://www.cubrid.org/files/attach/images/379199/127/651/image_thumb_5.png)
    4. Then click “Next”s and “Finish”s and restart Eclipse until the installation is completed.
3. Install Subversive 
    1. Go to “Help” ==> “Eclipse Market Place”
    2. Type "subversive" in the search field. Then click install below.  
    ![](http://www.cubrid.org/files/attach/images/379199/127/651/image_thumb_2.png)
    3. Select Subversive SVN JDK Ignore Extensions and Team Provider. Then select “Next” buttons until the installation is started.  
    ![](http://www.cubrid.org/files/attach/images/379199/127/651/image_thumb_3.png)
    4. Restart Eclipse and and select SVN Kit 1.7.8. Subversion repo provided by nGrinder is compatible with above SVN Kit 1.7.8.  
    Then click “Next”s and “Finish”s and restart Eclipse until the installation is completed.  
    ![](http://www.cubrid.org/files/attach/images/379199/127/651/image_thumb_1.png)  
    5. Now it’s time to connect Maven and Subversion by installing m2e subversive connector. Click File menu ==> New ==> Checkout Maven Projects from SCM. 
    ![](http://www.cubrid.org/files/attach/images/379199/127/651/image_thumb_6.png)  
    6. Then click m2e Marketplace in the next dialog.  
    ![](http://www.cubrid.org/files/attach/images/379199/127/651/image_thumb_7.png)  
    7. Check the m2e subversive and click Finish button.  
    ![](http://www.cubrid.org/files/attach/images/379199/127/651/image_thumb_8.png)  
    Then click “Next”s and “Finish”s and restart Eclipse until the installation is completed.
    8. Groovy JUnit Running requires the special JVM argument for every test run. It can be very annoying. If you plan to use this Eclipse instance only for nGrinder Test Case, it might be a good choice to provide the default JVM options for every JVM execution in this Eclipse.  
    Go To Window ==> Preference ==> Java ==> Installed JRE. Then click Edit on the default JRE.  
    ![](http://www.cubrid.org/files/attach/images/379199/127/651/pic3.PNG)  
    Finally you can provide the default VM arguments in the above field. The VM arguments you should provide here varies depending on the user configuration. Please check [Import Groovy Maven Project in IDE](import-groovy-maven-project-in-ide) to know which VM arguments you should provide.

Now your IDE is ready to run and debug your nGrinder groovy test cases. Go to  [Import Groovy Maven Project in IDE](import-groovy-maven-project-in-ide)