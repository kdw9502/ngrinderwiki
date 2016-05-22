The real user is not simple. There might have multiple usage scenarios for the target system. If you want to simulate this on The Grinder script, you may need to run multiple tests with different weight.

With following The Grinder script technique, it’s possible to simulate this case. You can upload the each scenarios in the **lib** folder(refer How to use library) and create the test script which control each test scenario like following.

##### Jython
```python
# Run test scripts in parallel
# Run TestScript1 in 50% of threads, TestScript2 in 30% of threads,
# and TestScript3 in 20% of threads.

from net.grinder.script.Grinder import grinder


# You can locate Scenario1,2,3.py files in the lib folder.
scripts = 〔"Scenario1", "Scenario2", "Scenario2"〕


# Ensure modules are initialised in the process thread.
for script in scripts: exec("import %s" % script)

def createTestRunner(script):
    exec("x = %s.TestRunner()" % script)
    return x

class TestRunner:
    def __init__(self):
        tid = grinder.threadNumber
        s = tid % 10
        if s in range(1,5):
            self.testRunner = createTestRunner(scripts〔0〕)
        elif s in range(6,8):
            self.testRunner = createTestRunner(scripts〔1〕)
        else:
            self.testRunner = createTestRunner(scripts〔2〕)

    # This method is called for every run.
    def __call__(self):
        self.testRunner()
```

##### Groovy
```groovy
@Test
public void test(){
    switch (grinder.threadNumber % 10) {
        case 1..5:
            doTest1()
            break
        case 6..8:
            doTest2()
            break
        default :
            doTest3()
            break
    }
}

public void doTest1() {
    ...
}

public void doTest2() {
    ...
}

public void doTest3() {
    ...
}
```
