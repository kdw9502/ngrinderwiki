nGrinder supports easy upgrade. Actually you don't need any additional step except just replacing nGrinder binaries. When nGrinder starts up, it upgrades the database automatically and use the ${NGRINDER_HOME}/.ngrinder folder content continuously.

### How to upgrade controller

Nothing is really necessary to upgrade controller. Just replace ngrinder-controller-{VERSION}.war in the your tomcat webapps folder. When you restart the tomcat, nGrinder detects the current database schema version and try to upgrade it to the version you're installing. Just it works.

### How to upgrade agent

From nGrinder 3.3 agent, the agent auto upgrade by controller command is supported.  See [Agent Auto Update](https://github.com/naver/ngrinder/wiki/agent-auto-update)
If you're running nGrinder agent in prior to 3.3 version,  If you'd like to upgrade the agent, just replace the current agent binary with the newer version. And run run_agent.sh or run_agent.bat.
That's it.