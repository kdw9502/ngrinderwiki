nGrinder has embedded SVN server to manage the user scripts file and resources. This will be very useful if you run a test with one script, and modify the script files. Then, in the test configuration page, the user can get the script content of that reversion, but not the modified one.   
The user id, is also the SVN user id, and the user can use any of the SVN tools to operate script files. nGrinder provides the function of uploading and creating script files.
 
![](http://www.cubrid.org/files/attach/images/379199/042/445/image_thumb.png)
 
If you make a script with the "Create a script button" shown above, you will see the following script.

![](http://www.cubrid.org/files/attach/images/379199/042/445/image_thumb_2.png)

You can freely edit the script as you wish. If you need to know the grinder script, please refer to this
While you edit your script, you can validate the script with the “Validate Script” button.

![](http://www.cubrid.org/files/attach/images/379199/042/445/image_thumb_3.png)

If I have included incorrect syntax at the end of a line, the Validate Script button will show the following result.

![](http://www.cubrid.org/files/attach/images/379199/042/445/image_thumb_4.png)

![](http://www.cubrid.org/files/attach/images/379199/042/445/image_thumb_7.png)

You can easily identify a script error with this validation feature.

In addition, If you need custom DNS entries, you can configure the host list by clicking the Add button.

![](http://www.cubrid.org/files/attach/images/379199/042/445/image_thumb_6.png)

If you add domain/ip, like “google.com” and “11.11.11.11” respectively, this script will access 11.11.11.11 when it tries to access google.com.

This is for nGrinder to work on the non-root environment which can not change /etc/hosts. nGrinder modifies the underlying JVM DNS resolution feature and makes it possible without root permission.