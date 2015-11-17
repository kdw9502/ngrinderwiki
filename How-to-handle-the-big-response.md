Some performance testing script may return a significant amount of response. This situation occurs when you try to download the file and test video in the script especially. Because nGrinder is configured to use at maximum 1GB memory per one process, the each thread can use 100MB if 10 threads per a process are invoked. Considering the variety of other overhead, the maximum response nGrinder can handle might be about 50 mega per request. if the each response is over this amount, nGrinder agent might cause OOM error.
 
The easiest way to solve this problem is to reduce the number of threads and to increase the number of processes. Instead of this solution, you can make script not to read whole response to save memory if the whole response read is not necessary. Please refer to the following code.

##### Jython 
```
from jarray import zeros
  
  
request1 = HTTPRequest()
# set this not to read whole response when the GET/POST is called.
request1.setReadResponseBody(False)
# Make any method call on request1 increase TPS
test1.record(request1)
  
  
class TestRunner:
    # initlialize a thread
    def __init__(self):
        grinder.statistics.delayReports=True
        pass
  
    # test method       
    def __call__(self):
        result = request1.GET("http://www.naver.com")
        # create a buffer..
        buf = zeros(1024, 'b')
        # read only 1024 byte from input stream
        stream = result.getInputStream();
        stream.read(buf)
        stream.close()
  
......
```

##### Groovy
```
@RunWith(GrinderRunner)
class Test1 {
 
    public static GTest test;
    public static HTTPRequest request;
 
    @BeforeProcess
    public static void beforeClass() {
        test = new GTest(1, "aa000000");
        request = new HTTPRequest();
        // Make not to read whole response.
        request.setReadResponseBody(false);
        test.record(request);
        grinder.logger.info("before process.");
    }
     
    // Prepare buffer
    byte[] buffer = new byte[1000];
 
    @BeforeThread
    public void beforeThread() {
        grinder.statistics.delayReports=true;
        grinder.logger.info("before thread.");
    }
 
 
    @Test
    public void test(){
        HTTPResponse result = request.GET("http://www.google.com");
 
        // Read only 1000 byte and close the stream
        def stream = result.getInputStream();
        stream.read(buffer);
        stream.close();
 
        if (result.statusCode == 301 || result.statusCode == 302) {
            grinder.logger.warn("Warning. The response may not be correct. The response code was {}.", result.statusCode);
        } else {
            assertThat(result.statusCode, is(200));
        }
    }
}
```