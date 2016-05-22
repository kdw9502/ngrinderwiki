In previous guide, we used a java library to provide the synchronized java client for socket.io app. In this guide, we will use python to archieve this goal.
The advantages to use python to implement the synchronized client for socket.io is, we can customize the event and communication easily in python just in nGrinder script editor. Not need to modify in java and export as a jar or class and upload into nGrinder.
So, at first, we need to add a script in the lib directory named “BlockingSocketIO.py”. It can be imported in test script to use from the lib directory. The script will be as below:
```python
# -*- coding:utf-8 -*-

from io.socket import IOCallback
from java.util.concurrent.locks import ReentrantLock
from io.socket import SocketIO

class BlockingSocketIO(IOCallback):

    respMsg = "";

    def __init__(self, url, logger):
        logger.info("init with url: %s" % url)
        self.transportLock = ReentrantLock()
        self.responseCondition = self.transportLock.newCondition()
        self.socketIO = SocketIO(url, self)
        self.logger = logger;

    def sendAndRcv(self, message):
        try:
            self.transportLock.lock()
            self.socketIO.send(message)
            self.respMsg = ""
            self.responseCondition.await()
            return self.respMsg
        except Exception, err:
            self.logger.error(err.message)
        finally:
            self.transportLock.unlock()
        return self.respMsg;

    def emitAndRcv(self, event, message):
        try:
            self.transportLock.lock();
            self.socketIO.emit(event, message)
            self.respMsg = ""
            self.responseCondition.await()
            return self.respMsg
        except Exception, err:
            self.logger.error(err.message)
        finally:
            self.transportLock.unlock()
        return self.respMsg;

    def onConnect(self):
        self.logger.info("connected")
    def onDisconnect(self):
        self.logger.info("disconnect")
    def onMessage(self, data, acknowledge):
        self.logger.info("message")
        setResponse(data)
    def on(self, event, acknowledge, args):
        self.logger.info("Server triggered event: %s" % event)
        for x in args:
            self.logger.info("args: %s" % x)
        respStr = ",".join(x.toString() for x in args)
        self.setResponse(respStr)
    def onError(self, exception):
        self.logger.error("error:%s" % exception.getMessage())


    def setResponse(self, data):
        self.logger.info("Server said: %s" % data)
        try:
            self.transportLock.lock()
            self.respMsg = data
            self.responseCondition.signal()
        finally:
            self.transportLock.unlock()
```

In this script, we implemented the java interface “IOCallback”. It is similiar with the java version of “BlockingSocketIO.java” of last guide.
And the test script is almostsame as that in java version, just be careful to import the BlockingSocketIO from the py module, but not the java lib.

```python
from net.grinder.script.Grinder import grinder
from net.grinder.script import Test

from org.json import JSONObject
import BlockingSocketIO

test1 = Test(1, "Test1")

class TestRunner:

    def testSocketIO(self):
        json = JSONObject()
        user = "Thread-%s" % grinder.threadNumber
        json.putOpt("user", user)
        msg = "test message<%s>." % user
        json.putOpt("message", msg)
        grinder.logger.info("msg:" + json.toString())
        respMsg = self.socketIO.emitAndRcv("user message", json)
        return respMsg

    def __init__(self):
        grinder.statistics.delayReports=True
        #init socket io, BlockingSocketIO.BlockingSocketIO() means from the module call that function to create a socketIO object.
        self.socketIO = BlockingSocketIO.BlockingSocketIO("http://127.0.0.1:3000", grinder.logger)

        #send socket.io server to init user
        json = JSONObject()
        user = "Thread-%s" % grinder.threadNumber
        json.putOpt("username", user)
        self.socketIO.emitAndRcv("user", json)

    # test method       
    def __call__(self):
        resp = self.testSocketIO()

        if "test message" in resp :
            grinder.statistics.forLastTest.success = 1
        else :
            grinder.statistics.forLastTest.success = 0

test1.record(TestRunner.testSocketIO)
```
