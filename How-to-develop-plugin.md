# How to develop Plugin Framework for Java (PF4J)

>It requires Spring and Java. Explicit build support is provided for Maven.

## Introduction
nGrinder, an opensource, had used Atlassian Plugin Framework (APF) to enable external developers to participate, but it was not that easy to develop on top of it. Therefore, [Plugin Framework for Java (PF4J)](https://github.com/pf4j/pf4j), which is easy to use, has been adopted from nGrinder 3.4.

* About PF4J : https://github.com/decebals/pf4j
* PF4J-UPDATE : https://github.com/decebals/pf4j-update
* nGrinder-plugins : https://github.com/naver/ngrinder-plugins


## PF4J Flow Chart
* The image below shows the overall PF4J flow chart.

![mindmap](https://raw.githubusercontent.com/wiki/naver/ngrinder/assets/pf4j-roadmap.png)

* The code below is the plugin which implements `OnLoginRunnable` interface.

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


## How to Use

1. nGrinder provides the following extension points.
 https://github.com/naver/ngrinder/wiki/Plugin-extension-points

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

    * Add ngrinder-core and PF4J dependencies to implement the extension points.

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
    
    * After extending PF4J plugin, inject the PluginWrapper in the constructor. And make the `createApplicationContext()` method return null. Because `ngrinder-core` project is a Maven Multi Module Projects. 
    Additional pf4j-springs were used to solve the dependency injection problem between ngrinder-controller and ngrinder-core projects.

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

4. Implements Extension Points
    1) Implement [nGrinder-core extension points](https://github.com/naver/ngrinder/wiki/plugin-extension-points) with inner classes.
    2) Add `@Extension` annotation to make it indexed in PF4J compilation.
        ```java
        @Extension
        public static class SiteminderSSOPluginExtension implements OnLoginRunnable {

            @Override
            public User loadUser(final String userId) {
                ....
            }

        }
        ```

    * By using `PluginManager::loadPlugins` method, you can load indexed files. Indexed files can be found in `target/classes/META-INF/extensions.idx`.

5. After build, put the generated jar file into `~/.ngrinder/plugins` directory and restart the nGrinder.

## Conclusion

  - When using the old Atlassian Plugin Framework (APF), there was difficulty in creating a compile with a separated build method called atlas-package, PF4J can be used in the existing maven build method.
  - We changed from APF to PF4J, and we were able to reduce resources about 4000KB to 80KB.
  - We want many users to develop nGrinder plugins freely with PF4J.
