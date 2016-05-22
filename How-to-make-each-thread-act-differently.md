nGrinder distributes the script into multiple agent machines and each agent invokes multiple processes  containing designated multiple threads per process. It follows basic The Grinder execution logic. Sometimes, some user asked me how to make each thread does diffent job. Usually, it’s to simulate the real world senario which a lot of users are involved or to eliminate the cache effect.

The most easiest way to achieve this is to use grinder.threadNumber property to get the current thread number. grinder.threadNumber returns the thread number starting from 0 within a single process. A script can make branches to act differently depending on this value. However if more than 100 threads are necessary, it’s better to combine the agent number, the process number and the thread number to generate the unique thread id. Following code shows how to do this.
```goorvy
def curNumber;
@BeforeThread
public void beforeThread() {
  def threadId = GrinderUtils.threadUniqId; 
}

@Test
public doTest() {
   // Make branches with curNumber.
}
```

In the beforeThread method, the curNumber is calculated with current test execution environment information. curNumber is unique within the test execution context. You can make branches in the doTest method to perform differently depending on the thread.
- [How to run the multiple tests with different weight]
- [How to run the multiple tests with different weight - Groovy Way]
