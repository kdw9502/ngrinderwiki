We could have increased the possible max vuser per agent by calibrating JVM options in nGrinder 3.2. The following is the result of our internal test. ‘O’ means that the test with the given vuser count per agent was executed successfully for 15 min. X means that the test was automatically stopped by the controller due to the lack of memory.

#### Groovy

|vuser per agent|1000|2000|3000|4000|5000|6000|
|---------------|----|----|----|----|----|----|
|1k response|O|O|O|O|O|X|
|10k response|O|O|O|O|O|X|
|100k response|O|O|O|O|O|X|
|process/thread count|10/100|10/200|10/300|10/400|10/500|10/600|

#### Jython

|vuser per agent|1000|1500|2000|2500|3000|4000|
|---------------|----|----|----|----|----|----|
|1k response|O|O|O|O|O|XXX|
|10k response|O|O|O|XOO|O|XXX|
|100k response|O|O|O|O|O|OO|
|process/thread count|10/100|10/150|10/200|10/250|10/300|10/400|

In summary, when using Groovy as a scripting language, it supports twice vuser count than using Jython. Even when selecting Jython, it still supports 5 times more vuser count than previous version.

#### Agent Machine Spec
Agent machine specs we used for this test are
- 2 Core, 4Gram VM (with 3.4Gram free)
- CentOS 5.3 64bit
- JDK 1.6u33 64bit
- nGrinder 3.2 agent

#### Reponse generating code
The following JSP code is used to simulate the responses. These generate 1k / 10k / 100k responses respectively.

##### 1k response.
```
<%
Thread.sleep(500);
for (int i = 1; i < 20;i++) {
    out.println("Hello!!! My Name is JunHo Yoon. Welcome! Perf Test!");
}
%>
```

##### 10k response.
```
<%
Thread.sleep(500);
for (int i = 1; i < 200;i++) {
    out.println("Hello!!! My Name is JunHo Yoon. Welcome! Perf Test!");
}
%>
```

##### 100k response.
```
<%
Thread.sleep(500);
for (int i = 1; i < 2000;i++) {
    out.println("Hello!!! My Name is JunHo Yoon. Welcome! Perf Test!");
}
%>
```