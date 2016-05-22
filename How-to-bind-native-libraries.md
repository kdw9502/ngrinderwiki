Some companies(especially game company) uses C/C++ more than java. They uses the native libraries for communication between clients and servers.  
They don’t know Java much which nGrinder is built on and want to reuse existing their native asset as much as possible.  
nGrinder has been shipped with JNA which enables java and native libraries(so, jar) binding. Therefore users can use DLL or SO files in their scripts.

The followings describes the step necessary to run so or dlls with nGrinder.

1. Prepare the DLLs or SOs which defines the simple functions which can be easily bridged to Java.  
   Methods with no parameter is pefered. If you want to pass the parameter from nGrinder script to DLLs or SOs, please make the parameter type simple as much as possible like char* or int.
2. Upload the created DLLs or SOs into lib folder. If you will define the script in the helloworld/TestRunner.groovy, you should upload those native libraries in the helloworld/lib/ folder.
3. Write the test script using JNA binding. Following show the simple binding between the groovy script and native lib named “dmclient”

   ```groovy
package org.ngrinder;

// import JNA
import com.sun.jna.Library
import com.sun.jna.Native

import static net.grinder.script.Grinder.grinder
import net.grinder.plugin.http.HTTPRequest
import net.grinder.plugin.http.HTTPPluginControl;
import net.grinder.script.GTest
import net.grinder.script.Grinder
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
// import static net.grinder.util.GrinderUtils.* // You can use this if you're using nGrinder after 3.2.3
import org.junit.Before
import org.junit.BeforeClass
import org.junit.Test
import org.junit.runner.RunWith

import HTTPClient.HTTPResponse
import HTTPClient.NVPair



@RunWith(GrinderRunner)
class TestRunner {
    // Define the interface which native library has.
    interface NativeClient extends Library {
        public void connect(  )
    }

    public static GTest test
    public static HTTPRequest request

    public static def nativeClient;
    @BeforeProcess
    public static void beforeProcess() {
        // load 'native_client.so or native_client.dll with interface NativeClient
        //(File extension(so or dll) is depending on agent's OS)
        nativeClient = Native.loadLibrary( 'native_client', NativeClient);
        test = new GTest(1, "Test1")
    }
    @BeforeThread
    public void beforeThread() {
        grinder.statistics.delayReports=true;
        // Record the current test instances method "doTest"
        // because the nativeClient instance is not recordable because it's the native method.
        test.record(this, "doTest");
    }

    @Test
    public void test(){
        doTest()
    }

    public void doTest() {
        // Call the native library's methods.
        nativeClient.connect();
    }
}
```
4. Then run the test.  
   Please be aware that you should run the SOs or DLLs in the machines which contains same library version(e.x. GLIBC) when you developed those.
