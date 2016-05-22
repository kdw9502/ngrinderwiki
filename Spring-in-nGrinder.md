nGrinder is based on Spring Framework 3.1 and uses it quite extensively, from the IoC feature to the latest Spring Cache.
This article describes the use of Spring Framework in nGrinder.

### Spring Data
- [[How to create dynamic queries in SpringData]]

### Spring Cache
nGrinder 2.X, a previous version, didn’t use 3rd-cache library, requiring developers to "reinvented the wheel" each time by creating a new cache implementation whenever they needed to achive a performance enhancement. In terms of configurability, this approache can be good. However it harms simplicity.

SpringCache has been newly introduced in nGrinder 3.X. It is easy to use, and very reliable. By putting @Cacheable annotation on the method, we can easily method return cacheable values so that continuous execution of complex and slow logic can be skipped.

The following is the nGrinder 3.0 Spring Cache configuration, defined in two files.
- /ngrinder-controller/src/main/resources/applicationContext-ehcache.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:cache="http://www.springframework.org/schema/cache" xmlns:task="http://www.springframework.org/schema/task"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
    http://www.springframework.org/schema/cache
    http://www.springframework.org/schema/cache/spring-cache-3.1.xsd">
    <cache:annotation-driven />

    <bean id="cacheManager" class="org.springframework.cache.ehcache.EhCacheCacheManager">
        <property name="cacheManager" ref="ehcache" />
    </bean>

    <bean id="ehcache" class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean">
        <property name="configLocation" value="classpath:ehcache.xml" />
        <property name="shared" value="true" />
    </bean>
</beans>
```

- /ngrinder-controller/src/main/resources/ehcache.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd">
    <defaultCache eternal="false" maxElementsInMemory="100" overflowToDisk="false" timeToIdleSeconds="900"
        timeToLiveSeconds="1800" />
    <cache name="users" maxElementsInMemory="100" eternal="false" overflowToDisk="false" timeToIdleSeconds="900"
        timeToLiveSeconds="1800" />
    <cache name="perftest" maxElementsInMemory="100" eternal="false" overflowToDisk="false" timeToIdleSeconds="900"
        timeToLiveSeconds="1800" />
    <cache name="perftestlist" maxElementsInMemory="100" eternal="false" overflowToDisk="false" timeToIdleSeconds="900"
        timeToLiveSeconds="1800" />       
    <cache name="file_entry_search_cache" maxElementsInMemory="100" eternal="false" overflowToDisk="false" timeToIdleSeconds="60"
        timeToLiveSeconds="60" />
</ehcache>
```

applicationContext-ehcache.xml is the spring configuration that enables eh-cache on spring bean methods. ehcache.xml is used for detailed configuration of cache. In the above xml, there are 4 caches, and they are calibrated based on the characteristics of each cache.

Now it’s time to use those caches on code.

```java
public class PerfTestService {
    /**
     * Save performance test with given status
     *
     * @param test
     * @param status
     * @return
     */
    @CacheEvict(value = { "perftest", "perftestlist" }, allEntries = true)
    public PerfTest savePerfTest(PerfTest test, Status status) {
        checkNotNull(test);
        test.setStatus(status);
        return perfTestRepository.save(test);
    }

    @Cacheable(value = "perftest")
    public PerfTest getPerfTest(long testId) {
        return perfTestRepository.findOne(testId);
    }

    @Cacheable(value = "perftest")
    public PerfTest getPerfTestCandiate() {
        return perfTestRepository.findOneByStatusOrderByCreatedDateAsc(Status.READY);
    }
....
}
```

In nGrinder 2.X, Finding Performance test candidates was managed in the our own cache, and there was little database access. We have deleted this code and simply just added @CacheEvict and @Cacheable annotations on methods. If we put such annotations on Spring Bean methods, this bean is proxied by the cache-enabling class(using CGLib). So the return value of the methods is kept in the proxy class. The proxy class uses the parameters as a key. For example, When I invoke getPerfTest(long testId), The proxy keeps the return value with the key given by testId. So, next time we call the same method with same testId, the cached PerfTest instance is returned (provided it is not expired or cache-evicted)

The cached value can be invalid if we have modified the corresponding PerfTest. In this case, @CacheEvict is used. In the above code, savePerfTest(PerfTest test, Status status) is modifying the PerfTest instance. So, there is a possibility that the cached value is not a valid one. With @CacheEvict, the proxy invalidates all cached entries in perftest cache and perftestlist cache, whenever this method is called.

You don’t need to make your own cache anymore with SpringCache supports, which is very convenient and easy to use. With some configuration, We can add a distributed cache service as well. Please be aware of this usage and use it continuously.

### Spring Task

Sometimes, it’s necessary to call a method in an asynchronous manner. If the method is very time-consuming and we have to respond to the user quickly, We usually keep it in a database and a Quartz  job handles the rest of the job of querying the database regularly. This is an approach used by many NHN developers.

But this approach is no logger necessary, now that we have SpringTask.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:task="http://www.springframework.org/schema/task"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
    http://www.springframework.org/schema/task
    http://www.springframework.org/schema/task/spring-task-3.1.xsd">

    <task:annotation-driven executor="myExecutor" scheduler="myScheduler" />
    <task:executor id="myExecutor" pool-size="1" />
    <task:scheduler id="myScheduler" pool-size="1" />
</beans>
```

From Spring 3.1, Spring task is supported. This is a simplification of the Executor service in java.concurrent package. To use it, you should setup executor and scheduler as shown above.

```java
@Service
public class FileEntryService {
    /**
     * Create user svn repo.
     *
     * This method is executed async way.
     *
     * @param user
     *            newly created user.
     */
    @Async
    public void prepare(User user) {
        File newUserDirectory = getUserRepoDirectory(user);
        try {
            if (!newUserDirectory.exists()) {
                svnClientManager.getAdminClient().doCreateRepository(newUserDirectory, user.getUserId(), true, true);
            }
        } catch (SVNException e) {
            LOG.error("Error while prepare user {}'s repo", user.getUserName(), e);
        }
    }
....
}
```

prepare(User user) method takes a long time, because it creates the SVN repository in the ngrinder data folder. It usually takes more than 1 second, But the UI doesn’t use the generated svn repository directly. For this reason, we put @Async annotation on it.  This method is called whenever a user is created. User creation response occurs very quickly, and the prepare method is run in the background’s myExecutor  I set.

In additions to this, we can easily make a scheduled job with @Scheduled annotation.

```java
/**
 * perf test run scheduler.
 *
 * @author JunHo Yoon
 * @since 3.0
 */
@Component
public class PerfTestRunnable implements NGrinderConstants {
    /**
     * Scheduled method for test execution.
     */
    @Scheduled(fixedDelay = PERFTEST_RUN_FREQUENCY_MILLISECONDS)
    @Transactional
    public void startTest() {
        PerfTest runCandidate = perfTestService.getPerfTestCandiate();
        if (runCandidate == null) {
            return;
        }
        // If there are too many trials, cancel running.
        if (runCandidate.getTestTrialCount() > PERFTEST_MAXIMUM_TRIAL_COUNT) {
            perfTestService.savePerfTest(runCandidate, Status.CANCELED);
        }
        doTest(runCandidate);
    }

....
}
```

Spring scans classes in the initialize process, and makes the startTest method executable whenever every fixedDelay is passed. Previously, Quartz did not support less than a 1 minute gap b/w execution. But @Scheduled supports gaps at the level of milliseconds. Even this is very stable, and supports @Transactional as well.


Please use those techniques extensively in nGrinder whenever Cache, Scheduler and Async service are necessary. It is always better to use these than creating one.

### IntelliJ
* TBD

Please enjoy this feature.
