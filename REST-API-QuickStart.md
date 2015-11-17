From nGrinder 3.2.3, nGrinder Controller supports 3 simple web APIs which a user can use to automate the test execution using the command line client (like curl).
```
# Clone the existing perftest and start
curl -u admin:admin http://host_name/perftest/api/226/clone_and_start
 
# See the existing perftest
curl -u admin:admin http://host_name/ngrinder-controller/perftest/api/226
 
# See the list of existing perftests
curl -u admin:admin http://host_name/ngrinder-controller/perftest/api
```

These APIs are protected by by HTTP Basic Auth. Therefore you should provide the ID / PW pair to access these.

### Clone the existing perf test and start
Before you invoke this API, There should be the existing test which was already successfully executed. You can clone and start this test using the following URL.

1. http://host_name/perftest/api/{test_id_to_be_cloned}/cloneAndStart (ngrinder 3.2.3)
2. http://host_name/perftest/api/{test_id_to_be_cloned}/clone_and_start (ngrinder 3.3~)

Then it will show the cloned test info like following.
```
{
  "testName": "Test for www.yahoo.com",
  "tagString": "",
  "description": "",
  "status": "READY",
  "ignoreSampleCount": 0,
  "targetHosts": "www.yahoo.com",
  "useRampUp": false,
  "threshold": "D",
  "scriptName": "www.yahoo.com/src/main/java/TestRunner.groovy",
  "duration": 60000,
  "runCount": 0,
  "agentCount": 2,
  "vuserPerAgent": 1,
  "processes": 1,
  "initProcesses": 0,
  "initSleepTime": 0,
  "processIncrement": 1,
  "processIncrementInterval": 1000,
  "threads": 1,
  "testErrorCause": "UNKNOWN",
  "progressMessage": "",
  "testComment": "",
  "scriptRevision": 70,
  "region": "NONE",
  "samplingInterval": 2,
  "param": "",
  "createdDate": "Oct 8, 2013 12:20:01 AM",
  "lastModifiedDate": "Oct 8, 2013 12:20:01 AM",
  "id": 380
}
```

You can provide the following arguments to modify some test attribute.
- agentCount=The count of agents which will execute this test. It should be b/w 0 and max agent count
- scriptRevision=the svn revision of the script. If the lastest should be used, provide –1 or don’t use this parameter

For example, http://host_name/perftest/api/11/clone_and_start?agentCount=2 will clone the 11th test with agent count 2.

A user can easily parse this message using command line json parser like [jq](http://stedolan.github.io/jq/) in the command line. If a user want to get the perf test id which is newly created, the command might be like below.
```
curl –u admin:admin –s http://host_name/perftest/api/123/cloneAndStart | jq “.id”
```

### See the existing perftest info
You can see the existing perf test info by calling…
>http://host_name/perftest/api/{test_id}

This will show the following info in the format of JSON.
```
{
  "testName": "Test for www.yahoo.com", <== The test name
  "tagString": "", <== tags for the test
  "description": "",
  "status": "CANCELED", <== current test status.
  "ignoreSampleCount": 0,
  "startTime": "Sep 30, 2013 6:49:30 PM",
  "finishTime": "Sep 30, 2013 6:50:02 PM",
  "targetHosts": "www.yahoo.com",
  "useRampUp": false,
  "threshold": "D",
  "scriptName": "www.yahoo.com/src/main/java/TestRunner.groovy",
  "duration": 60000,
  "runCount": 0,
  "agentCount": 1,
  "vuserPerAgent": 1,
  "processes": 1,
  "initProcesses": 0,
  "initSleepTime": 0,
  "processIncrement": 1,
  "processIncrementInterval": 1000,
  "threads": 1,
  "tests": 11,
  "errors": 0,
  "meanTestTime": 2094.82,
  "tps": 0.46,
  "peakTps": 1.0,
  "testErrorCause": "UNKNOWN",
  "progressMessage": "
Console is being prepared.
Console is started on port 11104
1 agents are starting.
1 agents are ready.
All necessary files are being distributed.
 - resource1.txt
 - TestRunner.groovy
 - logback-worker.xml
All necessary files are distributed.
The test is ready to start.
The test is started.
",
  "testComment": "",
  "scriptRevision": 70,
  "region": "NONE",
  "samplingInterval": 2,
  "param": "",
  "createdDate": "Sep 30, 2013 6:49:25 PM",
  "lastModifiedDate": "Sep 30, 2013 6:49:25 PM",
  "id": 376
}
```

For example, if you want to get the agent count of test 123, you can call
```
curl –u admin:admin –s http://host_name/perftest/api/123 | jq “.agentCount”
```

### See the lastest list of perf tests.
If a user want to check the list of perf tests, you can call like below.
```
curl –u admin:admin –s http://host_name/perftest/api?size=3
```

If you don’t provide any “size” param, it will return 1 test in the json array.

Above call will show the last 3 perf tests which exists in the ngrinder. This will returns the array of perftest json string. Therefore you can pick one of them using jq as well.
```
curl –u admin:admin –s http://host_name/perftest/api?size=10 | jq “.[4].agentCount”
```

See [Rest API](REST-API)