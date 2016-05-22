nGrinder 3.X uses ${user.home}/.ngrinder as a data and configuration storage.

You can easily attach that storage by autowiring Config class.

```groovy
@ContextConfiguration("classpath:applicationContext.xml")
public class ConfigTest extends AbstractJUnit4SpringContextTests {

    @Autowired
    public Config config;

    @Test
    public void testDefaultHome() {
        Home home = config.getHome();
        System.out.println(home);
        File ngrinderHomeUnderUserHome = new File(System.getProperty("user.home"), ".ngrinder");
        assertThat(home.getDirectory(), is(ngrinderHomeUnderUserHome));
        assertThat(home.getPluginsDirectory(), is(new File(ngrinderHomeUnderUserHome, "plugins")));
    }
}
```

You can modify nGrinder home by specifying the NGRINDER_HOME environment variable to ngrinder.home system property just before Config class is initialized.
