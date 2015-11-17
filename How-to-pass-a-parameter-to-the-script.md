From nGrinder 3.2.3, a user can set the test parameter in the test configuration page which can be passed into test.  
![](http://www.cubrid.org/files/attach/images/379199/305/788/image_thumb.png)

Parameter should be provided as 1~30 alphabet, numeric characters or underbar without spaces. When a test is executed, this are passed as a java parameter like –Dparam=value.

A script can take this by calling System.getProperty(“param”) and convert it to integer or boolean type whatever the script wants. However this is not detemined when a user valiadates the script. The param will be provided as a empty string, so a user should consider to call the System.getProperty with the default value like  below.

System.getProperty(“param”, “defaultValue”)

If you’re using groovy and use the ngrinder-groovy dependency (above 3.2.3), you can use the several convenient utitlit methods to parse the param in the script.

net.grinder.util.GrinderUtils class contains following static methods.
- getParam()
- getParam(String)
- getParamInt()
- getParamLong()
- getParamFloat()
- getParamDouble()
- getParamBoolean()

Whenever you need to get the param, you can call the one of above methods to get the param and parse it into the appropriate type.