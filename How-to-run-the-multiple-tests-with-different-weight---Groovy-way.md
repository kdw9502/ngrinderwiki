>This guide is applied from nGrinder 3.2.1. Please upgrade nGrinder if you want to use this. Your pom.xml in the Groovy script should be updated to point ngrinder-groovy 3.2.1 as well.

From nGrinder 3.2.1, nGrinder supports a special annotation "@RunRate" for Groovy script. You can assign this annotation on test methods to control the run frequency. @RunRate takes the run rate value in the unit of percentage(0..100). For example, @RunRate(10) means that this method is executed only 10% of total runs.

```
@RunRate(10)
@Test
public void doTest() {
   ... // This part will be executed only 10% of total runs
}
```

Be careful! the above number 10  is NOT the this method’s relative execution rate of total test methods. Each @RunRate annotated method is completely independent from other @RunRate annotated test methods. For example, assume there are two test methods annotated by @RunRate.

```
@RunWith(GrinderRunner)
class TestRunner {
    ...
    @RunRate(50)
    @Test
    void doTest1() {
           ...
    }
 
    @RunRate(20)
    @Test
    void doTest2() {
           ...
    }
}
```

nGrinder agent thread will try to execute the above test methods by loop. When nGrinder thread run 100 times, doTest1() will be executed only 50 times, and doTest2() will be executed only 20 times.  Precisely speaking, they will be executed in the following way.
- RUN 0 : None
- RUN 1 : doTest1()
- RUN 2 : None
- RUN 3 : doTest1()
- RUN 4 : None
- RUN 5 : doTest1(), doTest2()
- RUN 6 : None
- RUN 7 : doTest1()
- RUN 8 : None
- RUN 9 : doTest1()
- RUN 10 : doTest2()
-  ….

You might want to simulate this in your IDE by simulating the execution loop. Only you should do is to provide @Repeat annotation on the class level like following.
```
@Repeat(200)
@RunWith(GrinderRunner)
class TestRunner {
    ...
    @RunRate(50)
    @Test
    void doTest1() {
           ...
    }
 
    @RunRate(20)
    @Test
    void doTest2() {
           ...
    }
}
```

When you run above JUnit test in the IDE, you will find that doTest1() is executed 100 times (because it’s 50% of total run 200) and doTest2() is executed 40 times.

Enjoy nGrinder Groovy scripting.