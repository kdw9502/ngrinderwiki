Since we use JIRA to manage tasks,  we can add a Mylyn task repository for JIRA in eclipse, then we can easily manage task.
 
### Install JIRA connector for Mylyn 
Open “Mylyn task repository” view, and add a new task repository.

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_2.png)

If you have no connector for JIRA, click “Install more …” to install. Input “jira” and find the connector.

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb.png)

Click finish to begin the installation.

In the following dialog, check the following 2 items and click “Next” to install.

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_1.png)
 
### create Mylyn task repository 
After the installation is finished, open the “Add new Repo..” dialog to add new task repo.

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_3.png)

Select “JIRA” and click “Next”.

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_4.png)

In this dialog, input the “Server” and “Label” fields as given above, input your userId and password, and click “Validate …” to check. Then click "finish."

The following window will them be shown:

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_5.png)

If you want to check JIRA task in Mylyn task list, you need to create a query. So you should click “Yes” to create one.

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_6.png)

Here, we have created a query for the issuer “Assigned to me.” Click “Finish” and it will begin to get the tasks from JIRA.

Or, you can click “Update from repo…” to get the filters created in JIRA.

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_7.png)

After that, you can see the JIRA task in task view:

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_8.png)
 
### Manage JIRA task in eclipse
When we manage JIRA task with Mylyn in eclipse, the best practice is:

a) Activate one task in task list.
b) Open one file to modify for this task, or open some modified files for this task.
c) After we finish modifying, select the modified files and commit. In the commit log field, the log will be generated based on the template. Then after we commit and push, the JIRA task will be connected with the modified files in JIRA. This will be convenient for code review.
 
So, at first, we need to modify the commit template of Mylyn. Our format is :

${task.key} ${task.description}

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_9.png)

Then, select one task in the list, right-click on the task, and select “Activate” to make it the current working task.

Then, open the related files to modify. If you have already modified some files related with this task, we can just open the modified files. Then, it should be as given below, and only these files are listed.

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_10.png)
 
Then select the files you want to commit and commit, then the commit. The commit window will appear, and the commit log is generated.

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_11.png)

Input a description and commit. Then you can see the commit related to Github in JIRA.

![](http://www.cubrid.org/files/attach/images/379199/891/384/image_thumb_12.png)