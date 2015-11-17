One of the superiority of nGrinder is extensibility with java library in python. Because agent will execute the python script in jython, we can directly import java library in our test script. And here is the example to use jdbc library to perfoem DB load test.

Create jdbc test script:

Open the script list page and click “Create a script”:

![](http://www.cubrid.org/files/attach/images/379199/213/544/image_thumb_2.png)

Enter a name and select the checkbox to create lib directory. If you didn’t select it, you can also create the lib directory yourself.

After that, you can get a script based on a simple http test template. Then you can modify the script as below:
```
# -*- coding:utf-8 -*-
# Database test.
#
from java.sql import DriverManager
from net.grinder.script.Grinder import grinder
from net.grinder.script import Test
from cubrid.jdbc.driver import CUBRIDDriver
from java.util import Random
from java.lang import System
 
test1 = Test(1, "Database insert")
test2 = Test(2, "Database update")
random = Random(long(System.nanoTime()))
 
# Load the JDBC driver.
DriverManager.registerDriver(CUBRIDDriver())
 
def getConnection():
    return DriverManager.getConnection("jdbc:CUBRID:localhost:33000:ngrinder_cluster:::?charset=utf-8", "user", "password")
 
def ensureClosed(object):
    try: object.close()
    except: pass
     
# One time initialization that cleans out old data.
connection = getConnection()
statement = connection.createStatement()
# initialize the table should want to test.
statement.execute("drop table if exists ngrinder_update_temp")
statement.execute("create table ngrinder_update_temp(testid integer, test_number integer)")
 
ensureClosed(statement)
ensureClosed(connection)
 
class TestRunner:
    def __call__(self):
        connection = None
        updateStatement = None
        insertStatement = None
        try:
            # in this test, we will create connection and statement in every test transaction.
            connection = getConnection()
            updateStatement = connection.createStatement()
            insertStatement = connection.createStatement()
            insertStatement = test1.wrap(insertStatement)
            tmpId = random.nextInt(1024000)
            insertStatement.execute("insert into ngrinder_update_temp values(%d, %d)" %
                        (tmpId, random.nextInt(1024000)))
            updateStatement = test2.wrap(updateStatement)
            updateStatement.execute("update ngrinder_update_temp set test_number=%d where testid=%d" %
                        (random.nextInt(1024000), tmpId))
        finally:
            ensureClosed(updateStatement)
            ensureClosed(insertStatement)
            ensureClosed(connection)
```
 
Then you can click “Validate script” button to validate the syntex of the script. Be careful that the python needs some format requirement for indentation.

**NOTICE:**  
Don’t forget to save the script, because it will not be saved during validating.  


If there is no syntex error, then you will have an error about the driver can not be found. Then we need to upload the jdbc library into the “lib” directory. Open the script list page, and go into the lib directory. Click “Upload script or resources”:  
![](http://www.cubrid.org/files/attach/images/379199/213/544/image_thumb_3.png)  
Select the jdbc driver of the DB you want to test and upload.  
![](http://www.cubrid.org/files/attach/images/379199/213/544/image_thumb_4.png)

Then go back to your script and validate again. If you get the message as below, it means the script and library has no problem.
```
2013-01-05 11:06:08,622 INFO  The Grinder version 3.9.1
2013-01-05 11:06:08,628 INFO  Java(TM) SE Runtime Environment 1.6.0_38-b05: Java HotSpot(TM) 64-Bit Server VM (20.13-b02, mixed mode) on Linux amd64 2.6.18-238.el5
2013-01-05 11:06:08,633 INFO  time zone is CST (+0800)
2013-01-05 11:06:08,749 INFO  worker process 0
2013-01-05 11:06:08,916 INFO  instrumentation agents: traditional Jython instrumenter; byte code transforming instrumenter for Java
2013-01-05 11:06:13,335 INFO  running "jdbs-test.py" using Jython 2.2.1
2013-01-05 11:06:13,358 INFO  starting, will do 1 run
2013-01-05 11:06:13,359 INFO  start time is 1357355173359 ms since Epoch
2013-01-05 11:06:13,410 INFO  finished 1 run
2013-01-05 11:06:13,412 INFO  elapsed time is 53 ms
2013-01-05 11:06:13,412 INFO  Final statistics for this process:
2013-01-05 11:06:13,423 INFO  
             Tests        Errors       Mean Test    Test Time    TPS          
                                       Time (ms)    Standard                  
                                                    Deviation                 
                                                    (ms)                      

Test 1       1            0            3.00         0.00         18.87         "Database insert"
Test 2       2            0            0.50         0.50         37.74         "Database update"

Totals       3            0            1.33         1.25         56.60        

  Tests resulting in error only contribute to the Errors column.          
  Statistics for individual tests can be found in the data file, including
  (possibly incomplete) statistics for erroneous tests. Composite tests   
  are marked with () and not included in the totals.                      



2013-01-05 11:06:13,347 INFO  validation-0: starting threads
2013-01-05 11:06:13,423 INFO  validation-0: finished
```
 
Then create a test with this script:  
![](http://www.cubrid.org/files/attach/images/379199/213/544/image_thumb_6.png)

If you have monitor installed on that DB server, you can also set the target server. Then you can get the monitor data during the test.

When you select the script, the uploaded library should appear in resources area.

Then you can check the test running and report.

![](http://www.cubrid.org/files/attach/images/379199/213/544/image_thumb_7.png)

![](http://www.cubrid.org/files/attach/images/379199/213/544/image_thumb_8.png)
 
In the test above, we create connection in every transaction, so the mean time of every running is about half second.

Below is the test script and report if I get conenction at the beginning of the test and resue the connection. (I commentted some code about getting/closing connection in every transaction.)

##### Jython
```
# -*- coding:utf-8 -*-
# Database test.
#
from java.sql import DriverManager
from net.grinder.script.Grinder import grinder
from net.grinder.script import Test
from cubrid.jdbc.driver import CUBRIDDriver
from java.util import Random
from java.lang import System
 
test1 = Test(1, "Database insert")
test2 = Test(2, "Database update")
random = Random(long(System.nanoTime()))
 
# Load the JDBC driver.
DriverManager.registerDriver(CUBRIDDriver())
 
def getConnection():
    return DriverManager.getConnection("jdbc:CUBRID:localhost:33000:ngrinder_cluster:::?charset=utf-8", "user", "password")
 
def ensureClosed(object):
    try: object.close()
    except: pass
     
# One time initialization that cleans out old data.
 
connection = getConnection()
statement = connection.createStatement()
# initialize the table should want to test.
statement.execute("drop table if exists ngrinder_update_temp")
statement.execute("create table ngrinder_update_temp(testid integer, test_number integer)")
 
ensureClosed(statement)
#ensureClosed(connection)
 
class TestRunner:
    def __call__(self):
        #connection = None
        #updateStatement = None
        #insertStatement = None
        try:
            # in this test, we will create connection and statement in every test transaction.
            # connection = getConnection()
            updateStatement = connection.createStatement()
            insertStatement = connection.createStatement()
            test1.record(insertStatement)
            tmpId = random.nextInt(1024000)
            insertStatement.execute("insert into ngrinder_update_temp values(%d, %d)" %
                        (tmpId, random.nextInt(1024000)))
            updateStatement = test2.wrap(updateStatement)
            updateStatement.execute("update ngrinder_update_temp set test_number=%d where testid=%d" %
                        (random.nextInt(1024000), tmpId))
        finally:
            ensureClosed(updateStatement)
            ensureClosed(insertStatement)
```

##### Groovy
```
package org.ngrinder;
     
  import static net.grinder.script.Grinder.grinder
  import static org.junit.Assert.*
  import static org.hamcrest.Matchers.*
  import net.grinder.script.GTest
  import net.grinder.script.Grinder
  import net.grinder.scriptengine.groovy.junit.GrinderRunner
  import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
  import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
     
  import org.junit.Before
  import org.junit.BeforeClass
  import org.junit.Test
  import org.junit.runner.RunWith
     
  import groovy.sql.Sql
     
  /**
   * A simple example using the Groovy SQL that shows the retrieval of a
   * Simple query.
   */
  @RunWith(GrinderRunner)
  class TestRunner {
      public static GTest test
      public static sql
     
      @BeforeProcess
      public static void beforeProcess() {
          test = new GTest(1, "Test1")
          sql = Sql.newInstance( 'connection_string', 'id', 'password', 'cubrid.jdbc.driver.CUBRIDDriver' )
          test.record(sql);
          grinder.logger.info("before process.");
      }
     
      @BeforeThread
      public void beforeThread() {
          grinder.statistics.delayReports=true;
          grinder.logger.info("before thread.");
      }
     
      @Test
      public void test(){
          sql.eachRow( 'select * from code' ) { println "$it.s_name -- ${it.f_name} --" }
      }
  }
```

![](http://www.cubrid.org/files/attach/images/379199/213/544/image_thumb_9.png)