A user might want to use more agents than are available. For example, there might be 10 agents pre-installed, but the user can not use all 10 if admin sets a max agents per test of 5. Should admin increase the max agent value? If admin increases the max agents per test to 10, the actual current test can be only 1, and the other normal users will have to wait until this user finishes his test.

nGrinder solves this problem by providing user agents. If the specific user provides his own agents attached to a controller installed in his free machines, these agents are only used for that given user. So if the max agent is 5 and user provides 5 user specific agents, he or she is allowed to run 10 agents.

If you're running over nGrinder 3.3 and log in as a not admin account, you will see the following link in the menu. 

![](http://www.cubrid.org/files/attach/images/379199/885/088/001/image_thumb.png)

“HELLO” means here that it will download the private agent connecting to HELLO region. This will download the private agent for current user. 

This downloaded private agent already contains the configuration which the agent acts as private agent. Therefore all you need to do is just to run
```
# -o option make the configuration(__agent.conf) overwrite the existing configuration.
run_agent.sh -o
```
If you’re using nGrinder 3.2.X version, you need to set up the configuration by youself. The following describes how to set up the user own agents.

>
1. Install nGrinder agent in the user`s own machine following [installation guide's](https://github.com/naver/ngrinder/wiki/installation-guide) "install agent" section.
2. Open ~useraccount/.ngrinder-agent/agent.conf file  
   - In stand alone mode, and set up the following.
       - agent.console.ip=controller ip
       - agent.console.port= controller port
       - agent.region={anykeyword}_owned_{yourid}
   - In cluster mode, set up as following.
       - agent.console.ip=controller ip
       - agent.console.port= controller port
       - agent.region={controller region}_owned_{yourid}
3. Run agents.
4. Ask admin to approve these agents. These agents are not visible to users until the admin approves them .

Then run your agent. Finally you will have user own agents.