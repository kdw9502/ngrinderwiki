### Test List
User can create/stop/delete a test in this page. The ball color changes depending on the test status and it will refresh every 5 seconds. By hovering on it, you can see each test status.

![](http://www.cubrid.org/files/attach/images/379199/001/445/image_thumb_2.png)
 
### Test configuration
Test configuration is a critical aspect of a test. This sets the test duration, agent count, number of processes and threads etc in the test before running the test. Of course there are also more specific configuration items to satisfy a range of users. 

![](http://www.cubrid.org/files/attach/images/379199/001/445/image_thumb_3.png)

|Fields|Description|
|------|-----------|
|Agent|The number of agents should be used.|
|Vuser per agent|The number of virtual users in each agent , which is multiplied by process count and thread count.<br>You can configure them in greater detail by clicking the + button.<br>In the nGrinder stress test, all agents will have multiple processes and threads to simulate concurrent users.|
|Processes (Hidden)|The number of worker processes the agent should start.|
|Threads (Hidden)|The number of worker threads that each worker processe should start.|
|Script|Test script to be used.|
|Target Host|For every test, we can set the target host; it should be same as the host you access in your test script. nGrinder controller will start the monitoring task on the given target host when a test begins to run. Then, you can check the performance chart in the detailed test report.<br>We implemented customized DNS resolution feature with the target host. This means we can just input a domain and IP mapping here not touching /etc/hosts file. This will be useful for some http servers, which have no DNS domain.| 
|Duration|Specify how long the test will be executed.|
|Run Count|Specify how many test runs will be executed.|
|Ignore Sample Count|Specify How many samples will be ignored during TPS samplings.|

If you check "Enable Ramp-up", you can specify a gradual initialization of the process.

![](http://www.cubrid.org/files/attach/images/379199/001/445/image_thumb_4.png)

|Fields|Description|
|------|-----------|
|Ramp-Up|This means increasing the number of processes in every interval.|
|Initial Sleep Time||
|Initial Processes|This property sets the initial number of worker processes to start.|
|Processes Every|This property sets the interval in milliseconds at which the agent starts new worker processes.|

Please remember this is process ramp-up not thread ramp-up!!.. So you must have enough process count to increase the count gradually. If you set up 1 agent, 10 processes and 2 threads and you set the 1 in Ramp-Up, the count of vuser will be increased like 2, 4, 6, 8.... If you set 2 agents, it will be 4, 8, 12, 16...

The count of processes should be less than 10. Too many processes requires much agent memory. So you may not configure very smooth ramp-up. If you need to have more smooth ramp-up, please consider the [thread ramp-up](how-to-ramp-up-by-threads).

Currently, there will initially be a maximum of 10 tests running simultaneously by default, and one user can only run one test at the same time. All the agents will be assigned automatically to run some test. If there are not enough agents to run one test, that test will be delayed for several minutes, and then stopped as an error.  
When a user creates a test, the user can choose just to save the test or prepare it to run. If it is prepared to run, the test (including its configuration) cannot be modified. But the user can clone this test and create a new test with the same configuration.  
The test can also be scheduled to run later, rather than starting immediately.

![](http://www.cubrid.org/files/attach/images/379199/001/445/image_thumb_6.png)

![](http://www.cubrid.org/files/attach/images/379199/001/445/image_thumb_7.png)

After the test is started, the test running tab will be enabled and you can check the running status via this page.   
When the test is finished, you can then check the report and the detailed report. 
  
![](http://www.cubrid.org/files/attach/images/379199/001/445/image_thumb_8.png)

At the bottom right, you can download the log file for each agent. Using this file, you can identify script execution problems.