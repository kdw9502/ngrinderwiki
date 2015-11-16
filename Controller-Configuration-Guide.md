This chapter describes the advanced nGrinder controller configuration. You may not need to read this guide if you are not a system admin. However if you want to run nGrinder as PAAS, you should read this chapter.

### Controller Home

#### ${NGRINDER_HOME}
When nGrinder Controller starts, nGrinder will create ${user.home}/.ngrinder directory into user’s home directory. this directory contains the default configuration files and data. The following is .ngrinder folder's default location.
- Window   : C:\Users\${user.home}\.ngrinder
- Unix/Linux :${user.home}/.ngrinder

But if you like to assign the other directory for the home directory, please set Environment Variables ${NGRINDER_HOME}  before running ngrinder or you can provide "--ngrinder-home HOME_PATH" in the command line.

```
java -XX:MaxPermSize=200m -jar  ngrinder-controller-X.X.war --ngrinder-home /home/user/ngrinder
```

if you like to run multiple nGrinder controllers(each will handle only one network region) and make them work as one(cluster mode), you should make all controller share the same ${NGRINDER_HOME} content.  
It’s usually done by using NFS for ${NGRINDER_HOME path}. See [Cluster Architecture](cluster-architecture) for detail.

#### ${NGRINDER_EX_HOME}
${NGRINDER_EX_HOME} is used to specialize each controller in the cluster mode. By default, it’s set as ~/.ngrinder_ex.  
Not like ${NGRINDER_HOME}, ${NGRINDER_EX_HOME} is not automatically created when nGrinder starts up.
- Window   : C:\Users\${user.home}\.ngrinder_ex
- Unix/Linux :${user.home}/.ngrinder_ex

${NGRINDER_EX_HOME} is not the subject to be shared by multiple controllers. Each controller can have its own extended home. A user can define additional system configuration in the ${NGRINDER_EX_HOME}/ system.conf.  
Controller will load the system configuration from ${NGRINDER_HOME}/ system.conf first, Then, it will try to load the system configuration from ${NGRINDER_EX_HOME}/ system.conf and override it to sytem configuration from ${NGRINDER_HOME}/ system.conf  
For example, cluster.region configuration for each cluster member can be set in ${NGRINDER_EX_HOME}/system.conf file.  
When ${NGRINDER_EX_HOME} folder exists and the controller is started in the cluster mode, the controller will output the log into ${NGRINDER_EX_HOME}/logs/ ngrinder_{region_name}.log file.


### Command Line Options

#### Basic
If you run a controller without WAS. You can provide several options at the CLI interface.

|Name|Example|overriden properties|description|
|----|-------|--------------------|-----------|
|-p / –port|-p 80|-|HTTP port of the server. The default port is 8080.|
|-c / --context-path|-c ngrinder|-|Web context path of controller. The default context path is “”. For example, if you provide “ngrinner” here, the access url will be “http://localhost:8080/ngrinder”|
|-cm / --cluster-mode|-cm easy|cluster.mode|cluster mode. There are three availbale options(none / easy / advanced). The default value is “none”|
|-nh / --ngrinder-home|-nh ~/ngrinder|ngrinder.home|home path. By default it’s the “~/.ngrinder”|
|-exh / --exhome|-ex ~/ngrinder_ex|ngrinder.exhome|extended home path. By default it’s the “~/.ngrinder_ex” If you want to know what ngrinder extended home path is, See TBD.|
|-h / --help / –?|-h| |show help|
|-D|-Ddatabase=cubrid &database_url=blar|can override all|Dynamic properties. This options can override all the configuration defined in database.conf and system.conf|

#### Single mode
If you run ngrinder in none cluster mode (which means you didn’t provide “-cm” option at all), The following additional option is available.

|Name|Example|overriden properties|description|
|----|-------|--------------------|-----------|
|-cp / --controller-port"|-cp 9000|controller.port|controller port for agent connection.|

#### Easy cluster mode
Some companies uses multiple IDCs and they require the clustering (mulitple region support in a single nGrinder instance) feature. However nGrinder before 3.3 version requires Network File System to share ${NGRINDER_HOME} and Cubrid to have same DB by multiple controllers. We removes these requirements by allowing multiple controller installation in a single machine and allowing H2 TCP server connection. To understand easy clustering, we highly recommend to read our [Easy Clustering Guide](easy-clustering-guide).

You can run controller in easy cluster mode by the following command.
```
java -XX:MaxPermSize=200m -jar  ngrinder-controller-X.X.war -cm easy !<a href="/wiki_ngrinder/entry/additional-options" class="notexist">additional options</a>
```
The following mandatory option should be provided.

|Name|Example|overriden properties|description|
|----|-------|--------------------|-----------|
|-clh / --cluster-host|-clh 200.1.22.3|cluster.host|controller ip or hostname for agent connection for current region.|
|-clp / --cluster-port|-clp 10222|cluster.port|This cluster member's cluster communication port. Each cluster member should run with the unique cluster port|
|-cp / --controller-port|-cp 9000|controller.port|controller port for agent connection. Each cluster member should run with the unique controller port.|
|-r / --region|-region NORTH|cluster.region|Region name. Each cluster member should run with the unique region name.|
|-dt / --database-type|-dt h2|database.type|The database type. h2 and curbrid is available.|
|-dh / --database-host|-dh localhost|database.host|The database host name. The default value is localhost|
|-dp / --database-port|-dp 9092|database.port|The database port number. When cubrid is selected, the default is 33000 and h2 is slected the default value is 9092|

#### Advanced cluster mode
```
java -XX:MaxPermSize=200m -jar  ngrinder-controller-X.X.war -cm advanced
```
Advanced cluster mode does not have any options. It just activates the cluster mode then get the cluster configuration from ${NGRINDER_HOME}/system.conf or ${NGRINDER_EX_HOME}/system.conf files.

See [cluster realted configurations](Controller-Configuration-Guide#cluster-related-configurations) and [controller extended configurations](Controller-Configuration-Guide#ngrinder_ex_homesystemconf).

### Configurations
When a controller starts up, it copied default configurations into the ${NGRINDER_HOME}. You can modify them to set up the controller.

#### ${NGRINDER_HOME}/database.conf 
- This contains the database configuration. You can modify this file when you need to use Cubrid. By default, nGrinder uses H2 as a database.  
   ```
database=H2

database_username=admin

database_password=admin
```
- If you set only above options, H2 will create the DB in ${NGRINDER_HOME}/db/h2.db running as the embedded mode. In this case no ther process can not access this DB while runnning.
- If you have run H2 in the server mode not embedded mode by yourself. You should provide the database connection URL as well.  
   ```
database_url=tcp://{your_h2_server_host_ip_or_name}:{the_h2_server_port}/db/ngrinder
```
- If you like to use Cubrid, you need to set following configurations.  
   ```
database=cubrid

database_url={your_cubrid_host_ip_or_name}:{cubrid_port_maybe_33000}:{dbname} 
 
database_username=admin

database_password=admin
```
- NOTE: If you want to use the Cubrid DB High Availability feature. Please enables HA in cubrid following the guide and add the alternatives db address in database.conf  
   ```
database_url_option=&althosts={you_cubrid_secondary_host_ip_or_name}:{cubrid_port_maybe_33000}
```

#### ${NGRINDER_HOME}/system.conf 

##### Generic
- This contains controller configurations.
- You can modify these settings to calibrate the controller’s behavior.

|Key|Default|Compatible Keys (for ~nGrinder 3.2.X)|Description|
|---|-------|-------------------------------------|-----------|
|controller.verbose|false|verbose|Set true to see the more detailed log.|
|controller.dev_mode|false|testmode|Set true to run controller in dev mode. In the dev mode, the log goes to the default output(such as catalina.log in Tomcat) not ${NGRINDER_HOME}/logs/ and the security mode and cluster config verification are disabled. In addition, "agent force update" and "agent auto approval" is enabled. finally the script console is activated as well.|
|controller.demo_mode|false|demo|Set true to run controller in the demo mode. In the demo mode, each use does not allow to change the user password.|
|controller.security|false|security|Set true if security mode should be enabled. In the secutiry mode, nGrinder SecurityManager will be activated and limit the each test’s access to underlying resources/network in the agent. Please refer [Script Security](script-security)|
|controller.user_password_sha256|false|ngrinder.security.sha256|By default, nGrinder uses sha1 to encode passwords. If you like to use sha256, please set this true. However, you need to delete out all databases completly to apply the this configuration.|
|controller.usage_report|true|usage.report|Set false if you don't want to report ngrinder usage to google analytics. |
|controller.plugin_support|true|pluginsupport|Set false if the plugin should be de-activated. This is not the option applied on the fly. You need to restart the controller.|
|controller.user_security|false|user.security|Set true if you want to make some of the user profile fields(email, mobile phone) mandatory.|
|controller.allow_sign_up|false| |Set true if some users should be able to sign them by themselves. See [TBD](tbd)|
|controller.max_agent_per_test|10|agent.max.size|The maximum number of agents which can be attached per one test. This option is useful when you like to make nGrinder shared by many users. This configuration makes each test use only limited number of agents. For example, if you have 15 agents in total and you set 5 here, you can guarantee 3 users can run performance tests concurrently.|
controller.max_vuser_per_agent|3000|agent.max.vuser|The maximum number of vusers which can be used per one agent. In nGrinder, the vuser count means the total thread count. This should be carefully selected depending on the agent memory size. If you have the 8G RAM and 4 core agent, maybe the more than 10000 vusers can be executable. See TBD for our benchmark result.|
|controller.max_run_count|10000|agent.max.runcount|The maximum test run count for one thread. If you set this 10,000 and run 100 threads per an agent, the test can be executed 10,000 * 100 times at maximum.|
|controller.max_run_hour|8|agent.max.runhour|The maximum running hour for one test.|
|controller.max_concurrent_test|10|ngrinder.max.concurrenttest|The count of allowed maximun concurrent tests. If the more tests than specified here are started, some of them will be wating in the run queue.|
|controller.monitor_port|13243|monitor.listen.port|The monitor connecting port. The default value is 13243. When a perftest starts, the controller try to connect to the monitor in the specified target hosts for system statistics.|
|controller.url|auto-matically selected|ngrinder.http.url http.url|Controller URL (such as http://ngrinder.mycompany.com). This is used to construct the host name part of URL in the controller(such as SVN Link). If not set, the controller analyzes user request to represent URL text in the web page.|
|controller.controller_port|16001|ngrinder.agent.control.port|The port number to which each agent connects in the connection phase.|
|controller.console_port_base|12000|ngrinder.console.portbase|The base port number to which agents in the each test connects in the testing phase. If you allowed 10 concurrent tests by controller.max_concurrent_test=10 option, the ports from 12000 to 12009 are used for agents to connect the controller in the testing phase. You need to restart nGrinder to apply the this configuration.|
|controller.ip|all available IPs|ngrinder.controller.- ipaddress ngrinder.controller.ip|By default, the empty controller.ip configuration makes the controller bind to all available IPs in the current machine so that it can allow agents to be connected to all controller’s IP. Generally, It causes no problem. However, in the specialized env(such as EC2), more than 2 IPs(one for inboud and the other or outbound) are assigned. If you want to allow only one IP to be connected by the agent, you should put it here.|
|controller.validation_timeout|100|ngrinder.validation.timeout|Script validation timeout in the unit of sec. Increase this when you have the script which takes more than 100 secs for a single validation.|
|controller.enable_script_console|false| |true if the script console should be activated. Script console provides the way to directly access ngrinder internal.|
|controller.enable_agent_auto_approval|true| |false if the agent should be approved before using it. This option is useful when ngrinder is provided as PAAS.|
|controller.front_page_enabled|true| |Set false if the controller doesn’t have the internet access. this disables periodic RSS feed access to developer resources and QnAs.|
|controller.front_page_resources_rss|…|ngrinder.frontpage.rss|RSS URL for "Developer Resources" panel in the front page.|
|controller.front_page_resources_ more_url|…| |"More" URL for "Developer Resources" panel in the front page.|
|controller.front_page_qna_rss|…|ngrinder.qna.rss|RSS URL for "QnA panel" in the front page.|
|controller.front_page_ qna_more_url|…| |"More" URL for "Developer Resources" panel in the front page.|
|controller.front_page_ask_question_url|….|ngrinder.ask.question.url|"Ask a question" URL for "QnA panel" in the front page.|
|controller.help_url|…|ngrinder.help.url|The top most HELP link URL.|
|controller.default_lang|en|ngrinder.langauge.default|The default language if the user didn’t specify the langauge during login . This option is useful when you installs custom SSO plugin.|
|controller.admin_password_reset|false| |true if the admin password should be reset as "admin" while booting up. This option is useful when the admin lost its password. You should make it false after setting the admin password.|
|controller.agent_force_update|false| |true if the agents should always be updated when the update message is sent.  If it’s enabled, update will be performed even when the agent has later version than the controller’s.|
|controller.update_chunk_size|1048576| |The byte size of agent update message. by default, it’s set as 1048576(1MB). Agent update message contains the fragment of agent page and are sent to agents multiple times. If it’s bigger, the count of consequent messages are smaller so that it will be more speedy.|
|controller.safe_dist|false|ngrinder.dist.safe.region|true if you want to always enable the safe script transmission. If it is turned on, the each perf test starting speed will be slower but files are gurantee to be transmitted without errors.|
|controller.safe_dist_threshold|1000000|ngrinder.dist.safe.threashhold|If the files are bigger, the transmission error possibility is increased as well. nGrinder automatically enable safe script transmission by looking the file size. If you want to disable this, please make it 100000000.|

##### Cluster-Related Configurations
This file can have serveral cluster mode related options as well. Because ${NGRINDER_HOME}/system.conf should be shared by multiple controllers via NFS, 
some cluster related configurations which applies to all controllers in the cluster can be located here for easy administration.  
Followings are the options.

|Key|Default|Compatible Keys (for ~nGrinder 3.2.X)|Description|
|---|-------|-------------------------------------|-----------|
|cluster.enabled|false|ngrinder.cluster.mode|true if the cluster mode should be activated.|
|cluster.mode|none| |easy if you want to run the multiple controllers in a single machine.|
|cluster.members|-|ngrinder.cluster.uris|Comma or semi-colon separated list of all cluster member’s IP. For example) - 192.168.1.1;192.168.2.2;192.168.3.3|
|cluster.port|40003|ngrinder.cluster.listener.port|cluster communication port. When it’s the easy mode, each controllers in a cluster should have unique cluster port. However it’s the advanced mode, all cluter should have the  same cluster port.|

#### ${NGRINDER_EX_HOME}/system.conf
As we described here, ${NGRINDER_EX_HOME} is used to provide the specialized configuration per each controller in the cluster mode.  You can add additional system.conf file here as well and define several per controller configurations.

If you run controller in the single mode or easy cluster mode, you don’t need to create this file at all. However if you run controller in the advanced mode, you may need following configurations in ${NGRINDER_EX_HOME}/system.conf file.

|Key|Default|Compatible Keys (for ~nGrinder 3.2.X)|Description|
|---|-------|-------------------------------------|-----------|
|cluster.port|40003|ngrinder.cluster.listener.port|cluster communication port. When it’s the easy mode, each controllers in a cluster should have unique cluster port.  However it’s the advanced|
|cluster.host|-|cluster.ip|Console binding IP of this region. If not set, console will be bound to all available IPs.|
|cluster.region|NONE|ngrinder.cluster.region|The region name of this cluster member.|
|cluster.hidden_region|false|ngrinder.cluster.region.hide|true if you want to make this controller invisible from cluster members. This is useful when you like to run a private controller for administration.|
|cluster.safe_dist|false|-|true if tje file transmission in this region should be done by safer way.|

#### ${NGRINDER_HOME}/process_and_thread_policy.js
This file defines the logic to determine the appropriate combination of processes and threads for the given count of vusers.  
This file provides the flexible way to configure the appropriate processes and threads. Users usually don’t know which process and thread combination can result the best performance. Therefore, nGrinder let a user just input expected vuser per agent and configure the process and thread count automatically.  
The default logic is like following.
```
function getProcessCount(total) {
    if (total < 2) {
        return 1;
    }
     
    var processCount = 2;
 
    if (total > 80) {
        processCount = parseInt(total / 40) + 1;
    }
     
    if (processCount > 10) {
        processCount = 10;
    }
    return processCount;
}
 
function getThreadCount(total) {
    var processCount = getProcessCount(total);
    return parseInt(total / processCount);
}
```

By default, the more than 10 processes are not allowed, and more than 40 threads are allocated per process when 80 vusers are used.  
You can freely rewrite this methods(getProcessCount() / getThreadCount()) to fit your needs.  

#### ${NGRINDER_HOME}/grinder.properties
This file defines the default underlying "The Grinder" behavior. Some are overloaded by nGrinder at runtime but some are not. In most cases, the admin doesn't need to change this file.  
Please refer http://grinder.sourceforge.net/g3/properties.html for detail.

### Plugins
The folder contains plugins. If you like to install new plugins(~~.jar) or upgrade existing plugins, just drop them into into this folder. They will be automatically scanned and activated.  
If you like to delete out plugins, just delete out plugin files from there. That’s it.  
You can find out available plugins TBD.

### Home Folder Structure
In ${NGRINDER_HOME}, there are some folders to store data used in nGrinder. Followings are description for them.

|Folder Name|Description|
|-----------|-----------|
|logs|This store the nGrinder logs. nGrinder intercepts the tomcat log and saves the log in ngrinder.log file. This log contains only controller related logs. You can also monitor the content of this file through the admin menu.|
|perftest|This folder stores the each performance test related data.<ul><li>perftest/{test_id_range}/{test_id}/dist<ul><li>contains the files to be distributed.</li></ul></li><li>perftest/{test_id_range}/{test_id}/logs<ul><li>contains the logs from agents.</li></ul></li><li>perftest/{test_id_range}/{testid}/report<ul><li>contains the statistics for the test.</li></ul></li>|
|download|This folder contains the downloadable files like agent and monitor. For example, if you like to re-create the agent and monitor package, you can delete out the all contents in this folder.|
|plugins|This folder contains plugins. If you like to install new plugins(~~.jar) or upgrade existing plugins, just drop them into into this folder. They will be automatically scanned and activated.|
|repos|This folder contains the each user's svn repository.|
|script|This folder contains the script validation related resources. This is only used when the validation is performed.|
|db|This folder contains the H2 database data.|
|subversion|This folder contains default configuration for underlying svnkit.|
|webapp|This folder contains controller’s web application files when it’s executed with the embedded web server.|