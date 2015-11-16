From nGrinder 3.3, nGrinder support "easy" clustering mode which let multiple controllers run in a single machine. Because they runs in a single machine, no  network file system for ${NGRINDER_HOME} is not necessary. The running controllers will work with local ${NGRINDER_HOME}. Please follow the steps below.

### Run H2 TCP Server
1. Download H2 and unzip it.
   - http://www.h2database.com/h2-2014-01-18.zip
2. Run H2 TCP Server
   - bin\h2.bat or bin/h2.sh

### Run Controllers
1. Run controllers pointing different web(-p)/cluster(-clp)/controller(-cp) port and region name(-r). If they're over-wrapped,  the controllers fail to run due to the port conflict.  
   ```
java -jar -XX:MaxPermSize=200m -jar ngrinder-controller-X.X.war -p 8080 -cm easy -clp 10010 -r region1 -cp 9001      

java -jar -XX:MaxPermSize=200m -jar ngrinder-controller-X.X.war -p 8081 -cm easy -clp 10011 -r region2 -cp 9002       

java -jar -XX:MaxPermSize=200m -jar ngrinder-controller-X.X.war -p 8082 -cm easy -clp 10012 -r region3 -cp 9003       
```
   - For the CLI options, see Controller Configuration Guide
2. Visit one of the web port and login
    - http://localhost:8080/
3. And check the region is activated and all regions are up.  
   ![](http://www.cubrid.org/files/attach/images/379199/673/088/001/image_thumb.png)

### Run Agents
1. You can download and run the agent for each region. Just click the Download Agent > region Name like the above picture.  
   This will download the agent which is already configured to the region you specified.
2. untar  the agent in the separate machine.
3. Then run run_agent.sh -o or run_agent.bat -o
4. If you want to run multiple agents in a single machine, see [Agent Configuration Guide](https://github.com/naver/ngrinder/wiki/agent-configuration-guide)