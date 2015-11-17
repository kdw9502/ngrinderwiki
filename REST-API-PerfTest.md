### APIs

|URL|Method|Param / Desc / Required / Default|Description|
|---|------|---------------------------------|-----------|
|perftest/api|GET|page / Page Number / N / 1<br>size / Page Size / N / 1|Get the list of perf test|
|perftest/api|POST|Refer PerfTest model.|Create a perf test.<br>If you don’t provide much of perftest model parameter, they will be set as default value or emit the error.|
|perftest/api/{id}|GET|-|Get the perf test|
|perftest/api/{id}|PUT|Refer PerfTest model.|Update the perf test. Partial update is supported.|
|perftest/api/{id}?action=status|PUT|status / Status / Y / -|Update the status|
|perftest/api/{id}?action=stop|PUT|-|Stop the perf test|
|perftest/api/{id}|DELETE|-|Delete the perf test|
|perftest/api?action=stop|PUT|ids / comma separated id list / Y / “”|Stop the perf tests|
|perftest/api|DELETE|ids / comma separated id list / Y / “”|Delete the perf tests|
|perftest/{id}/api/sample|GET|-|Get the sampling data for given tests|
|perftest/api/status|GET|ids / comma separated id list / Y / “”|Get the status list for given tests|
|perftest/api/script|GET|-|Get the available  script list.|
|perftest/api/resource|GET|scriptPath / script path|Get the resource list for the given script.|
|perftest/api/{id}/status|GET|-|Get the status of the given test|
|perftest/api/{id}/logs|GET|-|Get the log file names of the given test|
|perftest/api/{id}/perf|GET|dataType/ comma separated data type key / Y / - <br>onlyTotal / total only? / N / false<br>imgWidth /  image width / Y / -|Get the perftest graph data.|
|perftest/api/{id}/monitor|GET|targetIP / test target IP / Y / - <br>imgWidth /  image width / Y / -|Get the monitor graph data|
|perftest/api/{id}/plugin/{plugin}|GET|imgWidth /  image width / Y / -	|Get the plugin graph data|
|perftest/api/{id}/clone_and_start|POST|Refer PerfTest model|cloen the given test and start|

### Models
PerfTest

|param|Default Value|Avaliable value|Description|
|-----|-------------|---------------|-----------|
|testName|-|-|test name|
|tagString|-|-|comma separated tag list|
|description|-|-|test description|
|status|SAVED|-|test status. Refer the Status model. Just SAVED / READY can be set by REST API.|
|scheduledTime|-|-|Test schedule. If not provided, which means "run now".<br>it should have "Tue Jan 28 2014 22:18:00 GMT+0900" style value|
|useRampUp|false|true / false|Use rampup?|
|rampUpType|PROCESS|PROCESS / THREAD|Ramp up type|
|threshold|D|D / R|Use duration (D) or use run time(R)?|
|scriptName|-|-|Script path|
|duration|60000|-|Duration in the unit of millisecond|
|runCount|0|-|Run count|
|agentCount|0|-|The count of agents|
|vuserPerAgent|1|-|The count of vusers per agent|
|processes|1|-|The count of processes|
|threads|1|-|The count of threads|
|samplingInterval|2| |The sampling interval|
|ignoreSampleCount|0|integer|Sampling|
|param|-|-|Test param|
|safeDistribution|false|true / false|Safe distribution?|
|rampUpInitCount|0|-|The initial count of rampup process/threads|
|rampUpInitSleepTime|0|-|The initial sleep time during rampup|
|rampUpStep|1|-|Ramp up step|
|rampUpIncrementInterval|1000|-|Ramp up interval|
|testComment|-|-|Test Comment|

### Status

|Status|Status Description.|
|------|-------------------|
|SAVED|Saved but not ready to test.|
|READY|Ready to test. If the test is ready, it wil be executed soon|
|START_CONSOLE|Below status are automatically tranfered from one to other  by controller.|
|START_CONSOLE_FINISHED|-|
|START_AGENTS|-|
|START_AGENTS_FINISHED|-|
|DISTRIBUTE_FILES|-|
|DISTRIBUTE_FILES_FINISHED|-|
|START_TESTING|-|
|TESTING|-|
|TESTING_FINISHED|-|
|ABNORMAL_TESTING|-|
|FINISHED|-|
|STOP_BY_ERROR|-|
|CANCELED|-|
|UNKNOWN|-|