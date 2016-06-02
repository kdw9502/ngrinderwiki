Plugin Framework for Java (PF4J)개발방법
=====================================

>이 문서는 한국 사용자들의 nGrinder PF4J plugin 개발 편의를 위해 작성 되었다.
>spring, java, maven으로 개발한다는 전재하에 작성 되었다.

도입 배경
=======
opensource인 nGrinder는 외부 개발자도 참여 가능하도록 Atlassian Plugin Framework(APF)를 제공하여 각 사용자들의 필요에 맞도록 plugin을 개발을 도모 하였으나,
그 활용도가 미미하여 접근하기 쉬운 Plugin Framework for Java (PF4J)를 nGrinder 3.4 버전부터 채택 하였다.

* PF4J 소개 : https://github.com/decebals/pf4j
* PF4J-UPDATE : https://github.com/decebals/pf4j-update
* login plugin repo : https://github.com/naver/ngrinder-siteminder-sso
* networkoverflow repo : https://github.com/naver/ngrinder-networkoverflow
* jvm monitor repo : https://github.com/songeunwoo/ngrinder-jvm-monitor-plugin

PF4J 흐름도
=========
* 아래 이미지는 전반적인 흐름도를 나타내었다.

![mindmap](https://raw.githubusercontent.com/wiki/naver/ngrinder/assets/pf4j-roadmap.png)

* 아래 소스 코드는 OnLoginRunnable을 구현한 plugin을 호출하여 처리를 해주는 부분이다.

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


개발 방법
=======

  1. nGrinder는 사용자들이 필요에 맞춰 자유로운 Plugin을 개발할 수 있도록 아래와 같은 확장 포인터들을 제공하고 있다.
 https://github.com/songeunwoo/ngrinder/wiki/How-to-develop-plugin

  2. pom.xml 설정
    - nGrinder repository를 pom.xml에 추가해준다.
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
    - 확장 포인터를 상속 받기 위해 ngrinder-core와 pf4j의 dependency를 추가한다.
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
    - pom.xml 파일에 해당 플러그인 검색을 위한 manifestEntries 설정 정보를 입력한다. 해당 입력 정보는 comfile시 아래 경로에서 확인할수 있다.
    ```
    target/classes/META-INF/MANIFEST.MF
    ```
    - 기재 되어있는 정보는 아래와 같다.
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
    - pom.xml 빌드 설정은 아래와 같이 JAR파일로 해주며, manifestEntries 설정은 아래를 참고하면 된다.

    ```xml
      <properties>
          <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
          <java.version>1.7</java.version>
          <ngrinder.core.version>3.4</ngrinder.core.version>
          <plugin.id>ngrinder-jvm-monitor-plugin</plugin.id>
          <plugin.class>org.ngrinder.pluginName.helloPlugin</plugin.class>
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

  4. 코드 작성시
    - 코드 작성시에는 PF4J plugin을 상속 받아준후 생성자로 PluginWrapper를 주입해 준다. 추가적으로 ngrinder-core project는 Maven Multi Module Project로 구성이 되어 있으므로
    createApplicationContext()는 null을 반환해 준다. ApplicationContext는 의존성 주입파트에서 다시 설명 하곘다.

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

    - 내부 클레스로 ngrinder-core의 확장 포인터들 중에서 구현하고자 하는 코드를 구현 한 후 @Extension 어노테이션을 주어 PF4J 컴파일시에 인덱스 될수 있도록 해준다.
    ```java
        @Extension
        public static class NetworkOverFlowExtension implements OnTestSamplingRunnable {

            @Override
            public User loadUser(final String userId) {
                ....
            }

        }
    ```

    - 인덱싱된 파일 정보는 아래 경로에서 확인 가능 하다. loadPlugins시 해당 정보를 참고하여 load가 된다.

    ```
    target/classes/META-INF/extensions.idx
    ```

  5. 빌드후 생성된 jar파일을 .ngrinder/plugins 폴더에 넣어준 후 ngrinder를 재 시작 한다.


customizing for ngrinder
========================
  1. 의존성주입
    - ngrinder-controller와 ngrinder-core 프로젝트간에 의존성 주입 문제를 해결 하고자 pf4j-spring을 추가적으로 사용하였다.
    ```xml
      <dependency>
          <groupId>ro.fortsoft.pf4j</groupId>
          <artifactId>pf4j-spring</artifactId>
          <version>0.2.0</version>
  		</dependency>
    ```

    - pf4j-spring의 SpringExtensionFactory를 상속받아 ngrinder의 ApplicationContext를 주입하여 사용하였다.

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

        아래는 Override 해준 코드이다.

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

  2. JAR파일 지원
    - 기존 PF4J는 컴파일된 폴더 파일 형식만 읽도록 되어 있었다. nGrinder에서는 개발의 편의성을 위하여 JAR파일을 읽을수 있도록 개선 하였다.

        pf4j의 AbstractExtensionFinder를 상속받아 NGrinderDefaultExtensionFinder에서 구현 하였으며,readPluginsStorages()에서 findResource를 찾을때에는 @Extension 어노테이션을 주어 생성해 놓은 "META-INF/extensions.idx" 파일을 참조 하였다.

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






마치며..
======

  - 기존 Atlassian Plugin Framework(APF)를 사용할 때에는 atlas-package 라는 별도의 빌드 방식으로 comfile을 해야하는 어려움이 있었지만, PF4J는 기존 maven빌드 방식 그대로 사용이 가능 하다.
  - APF에서 PF4J로 변경 하여 약 4000KB resource를 80KB로 감량 할수 있었다.
  - 많은 사용자분들이 PF4J를 활용하여, 필요에 맞춰 자유로운 Plugin을 개발 하였으면 한다.
