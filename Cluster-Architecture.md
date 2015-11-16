From nGrinder version 3.1, we introduced nGrinder controller clustering, which make the performance test can be executed by one of the clustered controller and support multiple set of agents(named region) to be used.  
Below is the architecture without cluster supports.

![](http://www.cubrid.org/files/attach/images/379199/772/545/image_thumb_4.png)

1. nGrinder uses an embedded svn server(SVNKit DAV) to manage script files. The svn repositories are stored in ${NGRINDER_HOME} directory.
2. nGrinder uses EhCache to improve the data retrieval performance from DB and SVN repositories.
3. nGrinder uses Spring Security to protect the system and uses Atlassian Plugin Framework for extensibility.

If you enable clustering mode by configuring the system.conf (See link) and install nGrinder into multiple machine, the system architect is changed as below:

![](http://www.cubrid.org/files/attach/images/379199/772/545/image_thumb_7.png)

1. All the controllers in the cluster shares the same DB and file system. All ngrinder controller should point same ${NGRINDER_HOME} folder which should be shared by NFS.
2. Each controllers can have its own special properties and log output folder. This information will be saved in ${NGRINDER_EX_HOME} in the each controller.
3. All controllers replicates the EhCache to each other to make some data to be visible in all controllers in cluster.

Each controller can serve nGrinder web content equally but handles the different test set depending the region. We don't provide any session clustering by default. So you may suffer login problem because sessions stored in a controller are not replicated to the other controllers. You may need to configure this by referring [Tomcat Session Clustering Guide](http://tomcat.apache.org/tomcat-6.0-doc/cluster-howto.html) or use the sticky session on L4. If you are not a expert on this, Just let users to connect only one controller. It's the easiest. :-)

How to configure the clustering?
- Check [Easy Clustering Guide](easy-clustering-guide)
- Check [Advanced Clustering Guide](advanced-clustering-guide)