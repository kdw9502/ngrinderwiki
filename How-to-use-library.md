### Jython and Groovy
Some guy may want to use java libraries or python py modules in the scripts. For this case, nGrinder supports upload of these files and a user can use external libraries without any system configuration. Just upload the jar or py files in the lib folder on the same location of the test script file. The files in the lib folder will be automatically passed to agents and be located in the agent classpath when the test is executed.  
![](http://www.cubrid.org/files/attach/images/379199/090/502/image_thumb.png)

Then You can just refer the uploaded jar or py like followings..
```
from net.grinder.script import Test
from net.grinder.script.Grinder import grinder
from org.json.simple import JSONValue
from java.util import Random
from java.lang import String
# This library is located in the lib folder.
from mpcCaller import NpcCaller
 
random = Random()Z
 
caller = NpcCaller("xx.xx.xx.15", 7090);
module = "tchat"
procedure = "chatBO/RelayCommand"…
```

### Groovy Maven Project
If you're using Groovy Maven Project you can easily add the external libraries in the pom.xml. Please refer [Groovy Maven Structure](groovy-maven-structure)