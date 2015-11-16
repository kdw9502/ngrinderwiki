> - This feature is available from 3.1
- This explain the advanced cluster configuration requiring NFS and Cubrid.
- If you want to run cluster without much effort, see [Easy Clustering Guide](easy-clustering-guide).

We enhanced nGrinder to support clustering from 3.1.

If you don’t know about nGrinder cluster, please refer to [Cluster Architecture](cluster-architecture). Following shows how to set up nGrinder cluster mode. Assuming you have been familiar with nGrinder in single instance mode. All nGrinder controllers should use the same DB and file system in cluster mode. What we should do at first is to make a shared folder on the NFS and make all controllers point this as ${NGRINDER_HOME}. And edit ${NGRINDER_HOME}/system.conf like followings.  
```
# if you want to enable ngrinder controller clustering. please enable below.
ngrinder.cluster.mode=true
 
# list of controller IPs
ngrinder.cluster.uris=xx.xx.xx.xx;xx.xx.xx.xx
 
# cache cluster listener port.
ngrinder.cluster.listener.port=40003
```

Next step is to create the each controller's specific information. For this ${NGRINDER_EX_HOME}/system-ex.conf file is used. Not like ${NGRINDER_HOME}, ${NGRINDER_EX_HOME} is not automatically created by nGrinder. Therefore you should make it in the local file system not to be shared by other controller. Create ${NGRINDER_EX_HOME}/system-ex.conf file and specify the region name here. The controller which reads this configuration will act as specified region controller.  
```
# Region setting of this controller.
# When running nGrinder in cluster mode, the ${NGRINDER_HOME} should be shared by other controllers.
# We expect this to be done by running ${NGRINDER_HOME} on NFS.
# However region setting can not be set on ${NGRINDER_HOME} because each controller in cluster must have its own configuration.
# Therefore, nGrinder supports one more configuration store folder ${NGRINDER_EX_HOME}
# Please locate following setting in the ${NGRINDER_EX_HOME}(by default ${user.home}/.ngrinder_ex)/system-ex.conf
ngrinder.cluster.region=Beijing
```

After finish this configuration, just restart all nGrinder controllers and login again. We will see this icon at headline!

![](http://www.cubrid.org/files/attach/images/379199/150/524/image_thumb_1.png)

If you want to put the L4 or load balancer in front of all controllers, you should set it up as sticky session so that each client connects only one controller. Otherwise, users may login again whenever they access difference controller.

After the controller configuration, you should also set the region name to all agents. As you should know, we should set the connecting controller IP  in each agents. Now there are several controllers. So you should choose one of them depending on what region you want this agent to serve for. You need to add the not only connecting controller IP but also region name this controller handles.

> - agent.console.ip=controller ip
- agent.console.port= controller port
- agent.region=controller region name

If this agent is User Agent, it should be like:
> - agent.console.ip=controller ip
- agent.console.port= controller port
- agent.region={controller region name}_owned_{userID}