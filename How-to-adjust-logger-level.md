Whenever a script calls the HTTP GET/POST method, each HTTP call is recorded at the log files in the agent's side. A log files are created per process and threads belonging to a process use a single log file. Logs can be huge(Giga byte size) depending on how many times the tests are executed and how many errors are occurred. However, nGrinder users need to check these logs to resolve their script problem which occurs in the agents. nGrinder solves this by limiting the maximum size of log file and passing only one process log to the controller. If the log file goes up to 1MB, nGrinder overwrites the existing log to reduce the logger overhead.

However, It might result the problem when a user tries to find out the error causes by checking agent logs. However, what if a user ran a test more than 1,000,000 times and 5 errors were occurred? Logs for 5 errors might have been overwritten by log rotation so the user can not find the clue for their problem,

For this case, you need to adjust log level to ERROR to focus on real error.

##### Jython
```python
# -*- coding:utf-8 -*-
  
# A simple example using the HTTP plugin that shows the retrieval of a
# single page via HTTP.
#
# @author admin
from net.grinder.script.Grinder import grinder
from net.grinder.script import Test
from net.grinder.plugin.http import HTTPRequest
from org.slf4j import LoggerFactory
from ch.qos.logback.classic import Level
from ch.qos.logback.classic import Logger
  
test1 = Test(1, "Test1")
request1 = test1.wrap(HTTPRequest())
  
class TestRunner:
    def __init__(self) :
        logger = LoggerFactory.getLogger("worker");
        logger.setLevel(Level.ERROR);
         
    def __call__(self):
        grinder.statistics.delayReports=True
        result = request1.GET("http://sample.com")
  
        
        if result.getStatusCode() == 200 :
            grinder.statistics.forLastTest.success = 1
        else :
            grinder.logger.error(result.getStatusCode())
            grinder.statistics.forLastTest.success = 0
```

##### Groovy
```groovy
import static net.grinder.script.Grinder.grinder
import static org.junit.Assert.*
import static org.hamcrest.Matchers.*
import net.grinder.plugin.http.HTTPRequest
import net.grinder.script.GTest
import net.grinder.script.Grinder
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
 
import org.apache.commons.io.IOUtils;
import org.codehaus.groovy.reflection.ReflectionUtils;
import org.junit.BeforeClass
import org.junit.Test
import org.junit.runner.RunWith
import org.slf4j.LoggerFactory;
 
import ch.qos.logback.classic.Level;
 
import HTTPClient.HTTPResponse
 
/**
 * A simple example using the HTTP plugin that shows the retrieval of a
 * single page via HTTP.
 *
 * This script is auto generated by ngrinder.
 *
 * @author ${userName}
 */
@RunWith(GrinderRunner)
class Test1 {
 
    public static GTest test;
    public static HTTPRequest request;
 
    @BeforeProcess
    public static void beforeClass() {
        test = new GTest(1, "test");
        request = new HTTPRequest();
        test.record(request);
        grinder.logger.info("before process.");
    }
 
     
    @BeforeThread
    public void beforeThread() {
                // Adjust logger to error level
        LoggerFactory.getLogger("worker").setLevel(Level.ERROR)
        grinder.statistics.delayReports=true;
        grinder.logger.info("before thread.");
    }
     
     
    @Test
    public void test(){
        HTTPResponse result = request.GET("http://www.google.com");
        if (result.statusCode == 301 || result.statusCode == 302) {
            grinder.logger.warn("Warning. The response may not be correct. The response code was {}.", result.statusCode);
        } else {
            assertThat(result.statusCode, is(200));
        }
    }
```