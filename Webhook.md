nGrinder 3.5.2 supports Webhook for specific events. A very simple configuration allows you to use it.  
Let's see how to use the nGrinder webhook.

# 1. Webhook settings

1. Go to the `webhook settings` menu

<img src="https://user-images.githubusercontent.com/14273601/94457997-4e28af00-01f0-11eb-8cb9-409fe5ee598f.png" width="195" height="380" />


2. Enter the settings below

| Name |  Description |
| --- | --- |
| **Payload URL** | The URL called when the webhook is triggered. All requests are requested by POST method |
| **Content Type** | Content type of webhook request, `application/json` and `application/x-www-form-urlencoded` are supported |
| **Start** | Determine if webhook is triggered at the start of the test. |
| **Finish** | Determine if webhook is triggered at the finish of the test. |
| **Active** | Determine if webhook is enabled or not |

<img src="https://user-images.githubusercontent.com/14273601/94458265-ae1f5580-01f0-11eb-9c90-a3dd9c1c943d.png" width="500" height="400" />


3. Check your URL is valid, the validation result will be added to recent activation automatically

<img src="https://user-images.githubusercontent.com/14273601/94460569-e07e8200-01f3-11eb-9e86-2e2703bcb106.png" width="850" height="175" />


# 2. Payload

#### 1. START 


| Filed | Type  | Description |
| --- | --- | --- |
| createdUserId  | String | User's ID who run the test |
| testName  | String |  Test name  |
|  scriptName | String | Script name |
|  startTime | String | Start time |
|  eventType | String | Type of the event that triggered the webhook |
|  tags | String | Tag names |
|  testId | long | Identification number |
|  vuser | int | Virtual users count |

#### 2. FINISH

| Field | Type | Description |
| --- | --- | --- |
| createdUserId  | String | User's ID who run the test |
| testName  | String |  Test name  |
|  scriptName | String | Script name |
|  finishTime | String | Finish time |
|  runTime | String | Total running time |
|  status | String | Finish status |
|  eventType | String | Type of the event that triggered the webhook |
|  tags | String | Tag names |
|  testId | long | Identification number |
|  errors | long | Errors count |
|  executedTests | long | Total executed test |
|  successfulTests | long | Successful test |
|  peakTPS | double | Top(Peak) TPS |
|  TPS | double | Average of TPS |
|  meanTestTime | double | Mean test time |
|  vuser | int | Virtual users count |


# 3. Check the result

<img src="https://user-images.githubusercontent.com/14273601/94462466-8501c380-01f6-11eb-8f77-db054bb78e61.png" width="715" height="540" />
