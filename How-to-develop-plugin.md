* This document is subject to be changed for ngrinder 3.4.

TBD


The following describes the plugin structure in nGrinder. nGrinder uses the [Atlanssian Plugin Framwork](https://developer.atlassian.com/display/PLUGINFRAMEWORK/Plugin+Framework) to customize nGrinder behavior without modifying nGrinder core code. nGrinder supports the following plugin extension points.

|Extension Interface|Extension Point Name(Plugin-Key)|Description|
|-------------------|--------------------------------|-----------|
|[OnLoginRunnable](https://github.com/naver/ngrinder/blob/master/ngrinder-core/src/main/java/org/ngrinder/extension/OnLoginRunnable.java)|on-login|loadUser is invoked whenever user info is required, Therefore if you like to allow not-yet-registered users to access ngrinder without explicit registration, you have to write the loadUser to load the user info from external user store procedure. Then this will invoke validateUser method to check  the user provides the valid password for the given id. If you have the external server which is able to validate the user with the given id and password, you can use it for the user validation. Default plugin embedded in nGrinder can be found in [here](https://github.com/naver/ngrinder/blob/master/ngrinder-controller/src/main/java/org/ngrinder/security/DefaultLoginPlugin.java).|
|[OnTestSamplingRunnable](https://github.com/naver/ngrinder/blob/master/ngrinder-core/src/main/java/org/ngrinder/extension/OnTestSamplingRunnable.java)|on-test-sampling|This is a place to invoke external logic which should be executed whenever the sampling is started, is performed, and stopped. For example, If you have to monitor external servers using your own logic, you can use this extension point to retrieve them. sampling method should be very fast. we're expecting sampling method should be done within 2 ms. If it takes more time to finish this method, it can eventually ruin the performance test sampling. So we're highly recommend the plugin implementer to invoke another thread which retrieves the data in the startSampling method and fetch the already retrieved data from this thread in sampling method. You can find the sample implementation [here](https://github.com/naver/ngrinder-networkoverflow/blob/master/src/main/java/org/ngrinder/network/NetworkOverFlow.java)|
|[OnControllerLifeCycleRunnable](https://github.com/naver/ngrinder/blob/master/ngrinder-core/src/main/java/org/ngrinder/extension/OnControllerLifeCycleRunnable.java)|on-start|This is the plugin extension point to invoke your logic when the start and shutdown nGrinder controller.|
|[OnPeriodicWorkingAgentCheckRunnable](https://github.com/naver/ngrinder/blob/master/ngrinder-core/src/main/java/org/ngrinder/extension/OnPeriodicWorkingAgentCheckRunnable.java)|on-working-agent-check|This is the plugin extension point to invoke your logic with the current agent status periodically. You can find the sample implementation here. This sample checks the current controllers whole agent's network usages and if it is over the designated traffics, stops all running test to block the network overflow.|
|[OnTestLifeCycleRunnable](https://github.com/naver/ngrinder/blob/master/ngrinder-controller/src/main/java/org/ngrinder/infra/plugin/OnTestLifeCycleModuleDescriptor.java)|on-test-start|This is the plugin extension point to invoke your logic when the test is executed and stopped.|
|[javax.servlet.Filter](http://docs.oracle.com/javaee/6/api/javax/servlet/Filter.html)|on-servletfilter|This is only one interface which ngrinder itself does not define. You should import as a servlet dependency in the plugin’s pom.xml to extend this interface.<div>&lt;!-- Servlet --&gt;<br>&lt;dependency&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&lt;groupId&gt;javax.servlet&lt;/groupId&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&lt;artifactId&gt;servlet-api&lt;/artifactId&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&lt;version&gt;2.5&lt;/version&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&lt;scope&gt;provided&lt;/scope&gt;<br>&lt;/dependency&gt;</div>The plugin will be located in the servlet filter chain. Therefore if you need to intercept the HTTPRequest or HTTPResponse to modify the WAS level behavior, you should implement this plugin. The sample implementation can be found [here](https://github.com/naver/ngrinder-siteminder-sso/blob/master/src/main/java/org/ngrinder/sso/SiteMinderLogoutFilter.java).|
|[javax.servlet.Filter](http://docs.oracle.com/javaee/6/api/javax/servlet/Filter.html)|on-preauth-servletfilter|This is the same extension point as on-servletfilter but it’s located before pre-auth by spring security is executed. The most common usage of this extension point is to allow SSO(such as SiteMinder) which is already processed by web servers like apache httpd. The sample implementation can be found [here](https://github.com/naver/ngrinder-siteminder-sso/blob/master/src/main/java/org/ngrinder/sso/SiteMinderFilter.java).|


- Copy the sample plugin from existing network overflow plugin
    - https://github.com/nhnopensource/ngrinder-networkoverflow
- Open pom.xml and modify groupId, artifiactId, and name for yours.
```
<groupId>place any plugin group id</groupId>
<artifactId>place any plugin artifact id</artifactId>
<version>place your plugin version number</version>
<name>place any prefered name</name>
```

- Import the plugin into eclipse or any your prefered IDE.
    - Ex) maven eclipse:eclipse
- Implement the plugin logic using above interfaces.
- Modify the atlassian-plugin.xml file to export your plugin.
    - Sample : https://github.com/nhnopensource/ngrinder-networkoverflow/blob/master/src/main/resources/atlassian-plugin.xml

```
<atlassian-plugin key="any key of this plugin" name="the plugin name" plugins-version="2">
    <plugin-info>
        <version>1.0.0</version>
    </plugin-info>
    <!—fix below plugin-key name with the plugin key you like to extend.
    <{plugin-key} key="any extension name" class="full class name of which extends the plugin interface" />
</atlassian-plugin>
```

- Package it with atals-package.sh or atlas-package.bat command and see the packaged jar file in the target folder.
- Copy this to ${NGRINDER_HOME}/plugins folder.
- That's it.
