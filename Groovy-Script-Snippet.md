##### HTTP POST Call
```
@Test
public void test1(){
    request1.POST("http://www.google.com", [new NVPair("key1","value1"), new NVPair("key2":"value2")] as NVPair[])
}
```

OR

```
@Test
public void test1(){
    request1.POST("http://www.google.com", nvs(["key1":"value1", "key2":"value2"]))
}
 
// Provide a method to convert map to NVPair array
def nvs(def map) {
    def nvs = []
    map.each {
        key, value ->  nvs.add(new NVPair(key, value))
    }
    return nvs as NVPair[]
}
```

##### How to assert test result
```
def result = request1.GET("http://www.google.co.kr")
assertThat(result.statusCode, is(200)) // result status code assetion
assertThat(result.text, containsString("google")) // result text assertion
```

##### How to record the @Test method.. not HTTPRequest. This is very useful to define a transaction.
```
@RunWith(GrinderRunner)
class TestRunner {
    static GTest test1
    static GTest test2
    @BeforeProcess
    public static void beforeProcess() {
        test1 = new GTest(1,"test1_statistics")
        test2 = new GTest(2,"test2_statistics")
        request1 = new HTTPRequest();
        HTTPPluginControl.getConnectionDefaults().timeout = 6000
        grinder.logger.info("before process.");
    }
 
    @BeforeThread
    public void beforeThread() {
        test1.record(this, "doTransaction1"); // Record current object’s doTransaction1 method into test1.
        test2.record(this, "doTransaction2"); // Record current object’s doTransaction2 method into test2.
        grinder.statistics.delayReports=true;
        grinder.logger.info("before thread.");
    }
 
    @Test
    public void doTransaction1(){
        // Do transactions here!!
    }
 
    @Test
    public void doTransaction2(){
        // Do another transactions here!!
    }
}
```

##### How to skip some initialization code for the each transaction.
```
@RunWith(GrinderRunner)
class TestRunner {
    static GTest test1
    @BeforeProcess
    public static void beforeProcess() {
        test1 = new GTest(1,"test1_statistics")
        request1 = new HTTPRequest();
        HTTPPluginControl.getConnectionDefaults().timeout = 6000
        grinder.logger.info("before process.");
    }
 
    @BeforeThread
    public void beforeThread() {
        test1.record(this, "doTransactionReal"); // Record current object’s doTransactionReal method into test1.
        grinder.statistics.delayReports=true;
        grinder.logger.info("before thread.");
    }
 
    @Test
    public void doTransaction(){
        doInit();
        doTransactionReal();
    }
      
    public doInit() {
        // This part won't affect the statistics
    }
 
    public void doTransactionReal(){
        // Do actual transactions here!!
    }
}
```

##### How to provide 0.5% RunRate.
```
@RunWith(GrinderRunner)
class TestRunner {
    static GTest test1
    @BeforeProcess
    public static void beforeProcess() {
        test1 = new GTest(1,"test1_statistics")
        request1 = new HTTPRequest();
        HTTPPluginControl.getConnectionDefaults().timeout = 6000
        grinder.logger.info("before process.");
    }
 
    @BeforeThread
    public void beforeThread() {
        test1.record(this, "doTransactionReal"); // Record current object’s doTransactionReal method into test1.
        grinder.statistics.delayReports=true;
        grinder.logger.info("before thread.");
    }
 
    def i = 0
 
    @RunRate(1) // Provide 1% run rate
    @Test
    public void doTransaction(){
        if (i < 1) {
            i++
            return // Do nothing when i = 0
        }
        i = 0
        doTransactionReal();
    }
      
 
    public void doTransactionReal(){
        // Do actual transactions here!!
    }
}
```