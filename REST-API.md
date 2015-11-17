nGrinder 3.3 provides more than 40 REST APis. Initially they were developed to run nGrinder as infrastructure. Recently one of the our partners started to create the gaming cloud and they wanted to run nGrinder but using their own UI. nGrinder 3.3 REST APIs supports this scenario. Our REST API set allows the other system to control full range of nGrinder features including User Management, Agent Management, Script Management, Performance Test Management. and even System Configuration.

You can see the [REST API/QuickStart](REST-API-QuickStart) first to see the how it works generally.

### URL and Method
nGrinder REST APIs are generally provided “/api” post fix of each available URLs. For example, the perf test related URL are started with http://hostname/perftest/, and the REST API URL for perf test starts with http://hostname/perftest/api/  
When {id} is added at the end of path like http://hostname/perftest/api/10 which means that “do sth on the element having the specified id”. Depending on what HTTP method is used, the URL acts the different job.

|URL|HTTP Method|Description|
|---|-----------|-----------|
|http://hostname/{topic}/api/|GET|Get the list of element|
|http://hostname/{topic}/api?ids={id,id}|GET|Get the given element|
|http://hostname/{topic}/api/{kind}?ids={id,id}|GET|Get the specific information of the given elements|
|http://hostname/{topic}/api/{id}|GET|Get the element|
|http://hostname/{topic}/api/{id}/{kind}|GET|Get the specific information of the given element|
|http://hostname/{topic}/api/|POST|Create the element|
|http://hostname/{topic}/api/{id}|PUT|Modify the element|
|http://hostname/{topic}/api?ids={id,id}|PUT|Modify the given elements|
|http://hostname/{topic}/api?action={action}&ids={id,id}|PUT|Do some action for the given elements|
|http://hostname/{topic}/api/{id}?action={action}|PUT|Do some action for the given element|
|http://hostname/{topic}/api/{id}|DELETE|Delete the element|
|http://hostname/{topic}/api?ids={id,id}|DELETE|Delete the given elements|

### Authentication
To use nGrinder REST API, it should be authenticated using HTTP Basic Auth. For example, when accessing the current perf test list through REST APIs, you can call APIs like below.
```
# when using curl
curl --user admin:admin http://ngrinder-hostname/perftest/api
# when using wget
wget --http-user=admin --http-passwd=admin http://ngrinder-hostname/perftest/api
```

### Parameters
When calling APIs, the parameter can be passed as well. For example, when you get the perf test list using
```
# get the second page of the perf test when the each page size is 10
http://hostname/perftest/api?page=2&size=10
```

With GET method, you can provide page and size parameter so that the given number of perf test list can be retrieved. When using DELETE method, the parameters should be provided as URL parameters. For POST, PUT, you should send them as the form variables.

### API Detailed Description
You can find detailed description for the each APIs.
- [REST API/PerfTest](REST-API-PerfTest)