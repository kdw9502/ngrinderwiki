Plugin Framework for Java (PF4J) 개발방법
=====================================

이 문서는 nGrinder 한국 사용자들의 PF4J개발 편의를 위해 작성 되었습니다. 기본적인 spring, java 개발을 안다는 전재하에 작성 되었습니다.

도입 배경
=======
>ngrinder는 Atlassian Plugin Framework(APF)를 제공하여
각 사용자들의 필요에 맞도록 plugin을 개발을 도모 하였으나,
그 활용도가 미미하여 접근하기 쉬운 Plugin Framework for Java (PF4J)를 nGridner 3.4 버전부터 채택 하였다.

* PF4J 소개 : https://github.com/decebals/pf4j

사용 방법

  1. .ngrinder/plugin 폴더내에 해당 jar파일 위치

개발 방법

  1. nGrinder는 사용자들이 필요에 맞춰 자유로운 Plugin을 개발할 수 있도록 아래와 같은 확장 포인터들을 제공하고 있다.
 https://github.com/songeunwoo/ngrinder/wiki/How-to-develop-plugin
  2. 확장 포인터를 상속 받기 위해 ngrinder-core와 pf4j dependency를 추가한다.

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

  3. pom.xml 파일에 해당 플러그인 검색을 위한 manifestEntries 설정 정보를 입력한다. 해당 입력 정보는 comfile시 아래 경로에서 확인할수 있다.
  ```
  target/classes/META-INF/MANIFEST.MF
  ```
  4. 코드 작성시에는 PF4J plugin을 상속 받아준후 생성자로 PluginWrapper를 주입해 준다.
  ```java
    public class UserLogin extends Plugin {

        public UserLogin(PluginWrapper wrapper) {
            super(wrapper);
        }

    }
  ```

  내부 클레스로 ngrinder-core의 확장 포인터들 중에서 구현하고자 하는 코드를 구현 한 후 내부 클레스에는 @Extension 어노테이션을 주어 PF4J 컴파일시에 인덱스 될수 있도록 해준다.
  ```java
      @Extension
      public static class UserLoginExtension implements OnLoginRunnable {

          @Override
          public User loadUser(final String userId) {
              ....
          }

      }
  ```
  인덱싱된 파일 정보는 아래 경로에서 확인 가능 하다.
  ```
  target/classes/META-INF/MANIFEST.MF
  ```
  5. 빌드후 생성된 jar파일을 .ngrinder/plugins 폴더에 넣어준 후 ngrinder를 재 시작 한다.


customizing for ngrinder
========================
  1. 의존성주입

  ngrinder-controller와 ngrinder-core 프로젝트간에 의존성 주입 문제를 해결 하고자 pf4j-spring을 추가적으로 사용하였다.
  ```xml
    <dependency>
        <groupId>ro.fortsoft.pf4j</groupId>
        <artifactId>pf4j-spring</artifactId>
        <version>0.2.0</version>
		</dependency>
  ```

  pf4j-spring을 사용하여 SpringExtensionFactory를 상속받아 ngrinder의 ApplicationContext를 주입하여 사용할수 있었다.

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

  2.





ngrinder  와 연동 하는 방법


마치며..
====
