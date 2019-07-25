# Plugin Framework for Java (PF4J) 개발방법

>이 문서는 한국 사용자들의 nGrinder PF4J plugin 개발 편의를 위해 작성 되었다.
>spring, java, maven으로 개발한다는 전재하에 작성 되었다.

## 도입 배경
opensource인 nGrinder는 외부 개발자도 참여 가능하도록 Atlassian Plugin Framework(APF)를 제공하여 각 사용자들의 필요에 맞도록 plugin을 개발을 도모 하였으나,
그 활용도가 미미하여 접근하기 쉬운 [Plugin Framework for Java (PF4J)](https://github.com/pf4j/pf4j)를 nGrinder 3.4 버전부터 채택 하였다.

* PF4J 소개 : https://github.com/decebals/pf4j
* PF4J-UPDATE : https://github.com/decebals/pf4j-update
* ngrinder-plugins : https://github.com/naver/ngrinder-plugins

## PF4J 흐름도
* 아래 이미지는 전반적인 흐름도를 나타냅니다.

    ![mindmap](https://raw.githubusercontent.com/wiki/naver/ngrinder/assets/pf4j-roadmap.png)

* 아래 소스 코드는 ```OnLoginRunnable```을 구현한 plugin을 호출하여 처리를 해주는 부분이다.
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


## 개발 방법
1. nGrinder는 사용자들이 필요에 맞춰 자유로운 Plugin을 개발할 수 있도록 아래와 같은 확장 포인터들을 제공하고 있다.
 https://github.com/naver/ngrinder/wiki/Plugin-extension-points

2. pom.xml 설정
    * nGrinder repository를 pom.xml에 추가해준다.
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

    * 확장 포인터를 상속 받기 위해 ngrinder-core와 pf4j의 dependency를 추가한다.
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

    * pom.xml 빌드 설정은 아래와 같이 JAR파일로 해주며, manifestEntries 설정은 아래를 참고하면 된다.
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

3. PluginWrapper 주입
    * 코드 작성시 PF4J plugin을 상속 받아준후 생성자로 PluginWrapper를 주입해 준다. 추가적으로 ngrinder-core project는 Maven Multi Module Project로 구성이 되어 있으므로 `createApplicationContext()`는 null을 반환해 준다. 
    ngrinder-controller와  ngrinder-core 사이의 ApplicationContext 의존성 주입 문제는 nGrinder에서는 pf4j-spring 을 이용하여 해결하였다.

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

4. 확장 포인터 구현
    1) ngrinder-core 의 [확장 포인터들](https://github.com/naver/ngrinder/wiki/plugin-extension-points) 중에서 구현하고자 하는 코드를 내부 클레스로 구현한다.
    2) @Extension 어노테이션을 추가해 PF4J 컴파일시에 인덱스 될 수 있도록 해준다. 

        ```java
            @Extension
            public static class SiteminderSSOPluginExtension implements OnLoginRunnable {

                @Override
                public User loadUser(final String userId) {
                    ....
                }

            }
        ```

    * 인덱싱된 파일은 ```target/classes/META-INF/extensions.idx```에서 확인이 가능 하다. ```PluginManager > loadPlugins()``` 에서 이 파일을 참고하기 위함이다.

5. 빌드후 생성된 jar파일을 ```.ngrinder/plugins``` 폴더에 넣어준 후 ngrinder를 재 시작 한다.

## 마치며..
  - 기존 Atlassian Plugin Framework(APF)를 사용할 때에는 atlas-package 라는 별도의 빌드 방식으로 comfile을 해야하는 어려움이 있었지만, PF4J는 기존 maven빌드 방식 그대로 사용이 가능 하다.
  - APF에서 PF4J로 변경 하여 약 4000KB resource를 80KB로 감량 할수 있었다.
  - 많은 사용자분들이 PF4J를 활용하여, 필요에 맞춰 자유로운 Plugin을 개발 하였으면 한다.
