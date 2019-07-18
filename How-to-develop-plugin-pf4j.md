How to develop Plugin Framework for Java (PF4J)
=====================================

>This document was written for the convenience of development of nGrinder PF4J plugin for Korean users.
>It was written under the premise of spring, java and maven development.

background of introduction
==========================
nGrinder, an opensource, has provided Atlassian Plugin Framework (APF) to enable external developers to participate,
The [Plugin Framework for Java (PF4J)](https://github.com/pf4j/pf4j), which is easy to access due to its limited utilization, has been adopted from nGrinder 3.4.

* About PF4J : https://github.com/decebals/pf4j
* PF4J-UPDATE : https://github.com/decebals/pf4j-update
* ngrinder-plugins : https://github.com/naver/ngrinder-plugins


PF4J Flow Chart
================
* The image below shows the overall flow chart.

![mindmap](https://raw.githubusercontent.com/wiki/naver/ngrinder/assets/pf4j-roadmap.png)

* The source code below is the part that processes by calling plugin which implements ```OnLoginRunnable```.

```java
....
    @Autowired
	private PluginManager pluginManager;

    @Override
	public UserDetails loadUserByUsername(String userId) {
		for (OnLoginRunnable each : getPluginManager().getEnabledModulesByClass(OnLoginRunnable.class, defaultPlugin)) {
			User user = each.loadUser(userId);
			if (user != null) {
			  .....
      	return new SecuredUser(user, user.getAuthProviderClass());
			}
		}
	}
....
```

* PluginManager :  https://github.com/naver/ngrinder/blob/master/ngrinder-controller/src/main/java/org/ngrinder/infra/plugin/PluginManager.java


How to use
===========

1. nGrinder provides the following expansion pointers to help users develop plugins that are free to meet their needs.
 https://github.com/naver/ngrinder/wiki/How-to-develop-plugin-extension-points

2. pom.xml setting

* Add nGrinder repository to pom.xml.

```xml
      <repositories>
          ....
          <repository>
              <id>ngrinder.maven.repo</id>
              <url>https://github.com/naver/ngrinder/raw/ngrinder.maven.repo/releases</url>
          </repository>
          ....
      </repositories>
```

* Add ngrinder-core and pf4j Dependency to inherit the extended pointer.

```xml
      <dependency>
          <groupId>org.ngrinder</groupId>
          <artifactId>ngrinder-core</artifactId>
          <version>${ngrinder.core.version}</version>
          <scope>provided</scope>
      </dependency>
      <dependency>
          <groupId>ro.fortsoft.pf4j</groupId>
          <artifactId>pf4j</artifactId>
          <version>${pf4j.version}</version>
      </dependency>
```

* In the pom.xml file, enter the manifestEntries configuration information to search for the plugin. The input information can be checked in the path below in the comfile.

```
    target/classes/META-INF/MANIFEST.MF
```

* Add plug-in metadata to the MANIFEST.MF file.

```
    Manifest-Version: 1.0
    plugin-Id: siteminder-sso
    Archiver-Version: Plexus Archiver
    Built-By: NAVER
    plugin-Provider: NAVER
    plugin-Version: 4.0.0
    plugin-Dependencies:
    plugin-Class: org.ngrinder.sso.SiteminderSSOPlugin
    Created-By: Apache Maven 3.0.5
    Build-Jdk: 1.8.0_91
```

* The pom.xml build settings are set in JAR file as shown below, and the manifestEntries settings can be found below.

```xml
      <properties>
          <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
          <java.version>1.8</java.version>
          <ngrinder.core.version>3.4</ngrinder.core.version>
          <plugin.id>siteminder-sso</plugin.id>
          <plugin.class>org.ngrinder.sso.SiteminderSSOPlugin</plugin.class>
          <plugin.version>1.0.0</plugin.version>
          <plugin.provider>NAVER</plugin.provider>
          <plugin.dependencies />
      </properties>

      <build>
          <plugins>
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-jar-plugin</artifactId>
                  <version>2.4</version>
                  <configuration>
                      <archive>
                          <manifestEntries>
                              <plugin-Id>${plugin.id}</plugin-Id>
                              <plugin-Class>${plugin.class}</plugin-Class>
                              <plugin-Version>${plugin.version}</plugin-Version>
                              <plugin-Provider>${plugin.provider}</plugin-Provider>
                              <plugin-Dependencies>${plugin.dependencies}</plugin-Dependencies>
                          </manifestEntries>
                      </archive>
                  </configuration>
              </plugin>
          </plugins>
      </build>
```

3. PluginWrapper injection
    
* Inherit PF4J plugin and inject PluginWrapper as the creator. And because ngrinder-core project Maven Multi Module Projects, createApplicationContext() returns null. I will explain ApplicationContext again in the Dependent Injection Part below.

```java
    public class NetworkOverFlow extends SpringPlugin {

        public NetworkOverFlow(PluginWrapper wrapper) {
          super(wrapper);
        }

        @Override
        protected ApplicationContext createApplicationContext() {
          return null;
        }

    }
```

4. Deployment Extension Pointer

    1) Implement [nGrinder-core extension pointers](https://github.com/naver/ngrinder/wiki/How-to-develop-plugin-extension-points) with inner classes.
    2) Add @Extension Annotation to make it indexable in PF4J compilation.

```java
    @Extension
    public static class SiteminderSSOPluginExtension implements OnLoginRunnable {

        @Override
        public User loadUser(final String userId) {
            ....
        }

    }
```

* Indexed files can be found in ``target/classes/META-INF/extensions.idx`. 
Indexed file is refered by ```PluginManager > loadPlugins()```.

5. After the build, put the generated jar file into the ```.ngrinder/plugins``` folder and restart the nGrinder.

customizing for ngrinder
========================

- Dependent injection

* Additional pf4j-springs were used to solve the dependency injection problem between ngrinder-controller and ngrinder-core projects.

```xml
    <dependency>
        <groupId>ro.fortsoft.pf4j</groupId>
        <artifactId>pf4j-spring</artifactId>
        <version>0.2.0</version>
    </dependency>
```

* The SpringExtensionFactory of pf4j-spring was inherited and used by injecting ngrinder's ApplicationContext.

```java
    pf4j-spring -  SpringExtensionFactory
    .......
    @Override
    public Object create(Class<?> extensionClass) {
        Object extension = createWithoutSpring(extensionClass);
        if (autowire && extension != null) {
            // test for SpringBean
            PluginWrapper pluginWrapper = pluginManager.whichPlugin(extensionClass);
            if (pluginWrapper != null) {
                Plugin plugin = pluginWrapper.getPlugin();
                if (plugin instanceof SpringPlugin) {
                    // autowire
                    ApplicationContext pluginContext = ((SpringPlugin) plugin).getApplicationContext();
                    pluginContext.getAutowireCapableBeanFactory().autowireBean(extension);
                }
            }
        }

        return extension;
    }
    ......
```

* Override code

```java
    @Component
    public class NGrinderSpringExtensionFactory extends SpringExtensionFactory {

        private final PluginManager pluginManager;

        @Autowired
        private ApplicationContext applicationContext;

        @Autowired
        public NGrinderSpringExtensionFactory(PluginManager pluginManager) {
        	super(pluginManager);
        	this.pluginManager = pluginManager;
        }

        protected void setApplicationContext(ApplicationContext applicationContext) {
        	this.applicationContext = applicationContext;
        }

        @Override
        public Object create(Class<?> extensionClass) {
        	Object extension = createWithoutSpring(extensionClass);
        	if (extension != null) {
        		PluginWrapper pluginWrapper = pluginManager.whichPlugin(extensionClass);
        		if (pluginWrapper != null) {
                applicationContext.getAutowireCapableBeanFactory().autowireBean(extension);
        		}
        	}
        	return extension;
        }

    }
```

  2. JAR File Support

* Existing PF4J is supposed to read only compiled folder file format. We have made improvements to read JAR files for ease of development.

```java
      NGrinderServiceProviderExtensionFinder
      ....
      private final String EXTENSIONS_RESOURCE_PATH = "META-INF/extensions.idx";

      @Override
      public Map<String, Set<String>> readPluginsStorages() {
          log.debug("Reading extensions storages from plugins");
          Map<String, Set<String>> result = new LinkedHashMap<String, Set<String>>();

          List<PluginWrapper> plugins = pluginManager.getPlugins();
          for (PluginWrapper plugin : plugins) {
              String pluginId = plugin.getDescriptor().getPluginId();
              log.debug("Reading extensions storages for plugin '{}'", pluginId);
              final Set<String> bucket = new HashSet<String>();

              URL url = ((PluginClassLoader) plugin.getPluginClassLoader()).findResource(EXTENSIONS_RESOURCE_PATH);
              if (url != null) {
                  Path extensionPath;
                  if (url.toURI().getScheme().equals("jar")) {
                      FileSystem fileSystem = FileSystems.newFileSystem(url.toURI(), Collections.<String, Object>emptyMap());
                      extensionPath = fileSystem.getPath(EXTENSIONS_RESOURCE_PATH);
                  } else {
                      extensionPath = Paths.get(url.toURI());
                  }
                  Files.walkFileTree(extensionPath, Collections.<FileVisitOption>emptySet(), 1, new SimpleFileVisitor<Path>() {

                      @Override
                      public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) throws IOException {
                          log.debug("Read '{}'", file);
                          Reader reader = Files.newBufferedReader(file, StandardCharsets.UTF_8);
                          ServiceProviderExtensionStorage.read(reader, bucket);
                          return FileVisitResult.CONTINUE;
                      }

                  });
              } else {
                  log.debug("Cannot find '{}'", EXTENSIONS_RESOURCE_PATH);
              }
            ....
```

Finally..
======

  - When using the old Atlassian Plugin Framework (APF), there was a difficulty in creating a comfile with a separate build method called atlas-package, PF4J can be used in the existing maven build method.
  - We changed from APF to PF4J, and we were able to reduce about 4000KB of resources to 80KB.
  - We want many users to develop plugin freely using PF4J.
