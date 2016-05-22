This chapter describes the advanced nGrinder agent configuration for nGrinder 3.3 or later. You may not need to read this guide if you run nGrinder for personal use. However if you want to run nGrinder as PAAS, you should read this chapter.

### Agent Download from Controller
from nGrinder 3.3, the agent packages are not distibuted from sourceforge. But it can be downloaded from the controller. This made big changes in agent configurations. The controller already knows how agents should be configured, it provides the agent package containning the appropriate configuration. For example when the admin user downloads an agent, the agent package contain the generic configurations. However if the general user downloads the package, the package will contain [[private agent]] configuration.

See [[Installation Guide]] to how to download the agent in the web browser.

If you prefer to download agents using wget, you can use following URLs directly as well.

|controller mode?|shared?|URL|
|----------------|-------|---|
|single|shared|http://{controller_host_name}/agent/download|
| |private|http://{controller_host_name}/agent/download?owner={user_id}|
|cluster|generic|http://{controller_host_name}/agent/download?region={region_name}|
| |private|http://{controller_host_name}/agent/download/{region_name}/{user_id}|

If you installed clustered controllers in the helloworld.com and want to download private agent for “hello” user and “region1” region, you can download it using following URL.
```
wget http://helloworld.com/agent/download/region1/hello
```
If you don’t like to composite URL, just copy the link in the browser and paste it.

The The pre-setup configuration are stored as '''./__agent.conf''' file in the agent package. When the agent starts up, this __agent.conf file will be copied into the ${NGRINDER_AGENT_HOME}/agent.conf if this file does not exists. If there is already ${NGRINDER_AGENT_HOME}/agent.conf, agent will only show the warning message and skip the copy action. Then it will be executed with the preivous agent.conf.

If you like to always overwirte the existing agent.conf with __agent.conf, you should run an agent with –o option which forces to overwrite the agent.conf. See agent's [Command Line Options](#command-line-options)

### Agent Start / Stop

nGrinder agent has several command line shells. You can run one of them to invoke or kill the agent process.
- run_agent.sh or run_agent.bat
    - use this to start the agent.
- run_agent_bg.sh
    - use this to start the agent in the backgroud in linux.
- stop_agent.sh or stop_agent.bat
    - use this to stop the currently running agent.

### Agent Home
When an agent starts, it will create ${user.home}/.ngrinder_agent directory into user’s home directory. this directory contains the default configuration files and data. The following is .ngrinder_agent folder's default location.
- Window   : C:\Users\${user.home}\.ngrinder_agent
- Unix/Linux :${user.home}/.ngrinder_agent
But if you like to assign the other directory for the home directory, please set Environment Variable ${NGRINDER_AGENT_HOME}  before running an agent or you can provide "--ngrinder-agent-home NGRINDER_AGENT_HOME” or “-nah NGRINDER_AGENT_HOME" in the command line.
```
./run_agent.sh --ngrinder-agent-home /home/user/ngrinder_agent
```

### Running multipe agents in a single machine
If you like to run multiple agents in a single machine, you should run agents under the below constaints.
1. Each agent should run with **the different binary location**. When updating agent by controller, the each agent get the updated packageand try to replace current binary, When more than two agent instance from a single binary. the updating process will be mixed up and mass up the agent’s binary. If you don’t use Agent Auto Update feature, you can skip this.
2. Each agent should run with **the different agent home path**. As descirbed “Agent Home” section, it contains the each agent’s specific configuration and runtime data. So it’s important to specify the different home path per agent.
3. Each agent should run with **the different host id**. controller differenciates the agents by ip and host id combination and host-id is automatically resolved by calling InetAddress.getLocalHost().getHostName() in java. Therefore, controller think only one agent is running when two agent’s ip and host ids are same. To avoid this, you should provide the custom host-id at at least one of the agents.

To satisfy above constraints, you can run agents like below.
1. Install multiple agent binaries as much as you like to run.
    - 1st  : ~/apps/ngrinder_agent_1
    - 2nd : ~/apps/ngrinder_agent_2
2. run each agent using following command
    - 1st : cd ~/apps/ngrinder_agent-1;   ./run_agent.sh --agent-home ~/.ngrinde-agent-1 --host-id first-agent
    - 2nd : cd ~/apps/ngrinder_agent-1;   ./run_agent.sh --agent-home ~/.ngrinde-agent-2 --host-id second-agent

### Command Line Options

nGrinder agent’s run_agent.sh commands takes several parameters to override the existing configurations in ${NGRINDER_AGENT_HOME}/agent.conf .

|Name|Example|overriden properties|description|
|----|-------|--------------------|-----------|
|-o, --overwrite-config|-o|-|Provide this If you want to overwrite the existing ${NGRINDER_AGENT_HOME}/agent.conf with current __agent.conf in the binary directory. This is the useful when you like to the existing agent to connect to the other controller.|
|-ah / –agent-home|-ah ~/.ngrinder-agent1|ngrinder.agent.home|agent home path. By default it’s the ~/.ngrinder_agent|
|-ch / --controller-host|-ch 10.10.10.10|agent.controller_host|the controller host name or IP to which this agent is connect.  If it’s not specified, ${NGRINDER_AGENT_ HOME}/agent.conf’s agent.controller_host value is used.|
|-cp / --controller-port|-cp 10023|agent.controller_port|the controller port number.  If it’s not specified, ${NGRINDER_AGENT_ HOME}/agent.conf’s agent.controller_port value is used.|
|-r / --region|-r region_owned_myid|agent.region|the controller’s region name. If it should be private agent, it should be {region_name}_owed_{userid}. If it’s the controller is not cluster mode, you can omiit {region_name}.|
|-hi / --host-id|-hi my_agent_1|agent.host_id|the agent host id. If not provided, host name is automatically selected.|
|-s / --silent|-s|common.silent_mode|Make the agent output have only very important messages.|
|-v / --version|-v| |Show the agent’s version number and exit.|
|-D|-D|can override all|Dynamic properties. This options can override all the configuration defined in agent.conf|
|-h / --help|-h| |Show usage.|

### Configurations
When an agent starts up, it copied controller providing configurations into the ${NGRINDER_AGENT_HOME}. If there are already such configuration, it emits the warning message and skip the copy process.  agent.conf contains the base configuration when an agent runs. If the command line options are provided, provided options will override the some configuration specified this file.

### ${NGRINDER_AGENT_HOME}/agent.conf

|Key|Default|Compatible Keys (for ~nGrinder 3.2.X)|Description|
|---|-------|-------------------------------------|-----------|
|common.start_mode|agent|start.mode|the mode in which agents run. agent and monitor can be used here. You don’t need to specify this at all.|
|common.verbose|false|verbose|Set true if you make agent output very detailed log into ${NGRINDER_AGENT_HOME}/log/agent.log file.|
|common.silent_mode|false|-|Set true to make the agent log have only very important messages|
|agent.controller_host|localhost|agent.controller.ip, agent.console.ip, agent.controller_ip|the controller host name or IP to which this agent is connect.|
|agent.controller_port|16001|agent.controller.port, agent.console.port|the controller port number.|
|agent.region|-| |the controller’s region name. If it should be private agent, it should be {region_name}_owed_{userid}. If it’s the controller is not cluster mode, you can omiit {region_name}.|
|agent.host_id|localhost’s hostname|agent.hostid|the agent host id. If not provided, host name is automatically selected.|
|agent.server_mode|true| |false if you want to remove –server vm option in agent’s process run.|
|agent.all_logs|false|agent.send.all.logs|true if you want to send the all agent’s processes log to controller when tests is finished. By default, it sends only one process’s log to avoid useless duplicated info.|
|agent.limit_xmx|true| |false if you want to make each test’s process use be able to use 1GB memory. By default, agent process can not use more than 1GB because the more memory means longer gabage collection time.|
|agent.java_opt|-|agent.javaopt|Provide the additional java vm options which will be used when running each test’s process.|
|agent.keep_logs|false|	|true if you want to keep the test’s logs even after a test is finished. By default, it removes all logs at the end of each test.|

### Agent Home Folder Structure
In ${NGRINDER_AGENT_HOME}, there are some folders to store data used in an agent. Followings are description for them.

|Folder Name|Description|
|-----------|-----------|
|log|This stores the agent log.|
|file-store|This constains the each users lastest script and resources. To speed up the file distribution, the files are not deleted and kept here even after each test’s end as a cache.|
|native|This contains the native library to collect agent’s system usage.|
