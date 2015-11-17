Sometimes it’s necessary to analyze data returned from server and  put the value to another request. Actually, we don’t recommend to use “Parsing” because it’s very expensive processing and eventually decrease the whole agent performance. It’s always better to use regular expression to extract some data from the returned data. However if you really need to parse XML, you can do it easily.

##### Jython
You can use ElementTree parser which nGrinder 3.1.1 and above has.
```
from xml.etree import ElementTree
xmlstring = """
<hello>
    <world>
        WOW
    </world>
    <my>
        <ngrinder>
            GOOD!!
        </ngrinder>
    </my>
</hello>"""
mydom = ElementTree.fromstring(xmlstring)
# XPATH
grinder.logger.info(mydom.findall([0].text)
```

The result will be “GOOD"!!”. Please refer http://docs.python.org/2/library/xml.etree.elementtree.html for the detailed API usage.

##### GROOVY
```
def message = """
    <hello>
    <world>
        WOW
    </world>
    <world a="wow_attr">
        WOW2
    </world>
    <my>
        <ngrinder>
            GOOD!!
        </ngrinder>
    </my>
</hello>"""
def hello = new XmlParser().parseText(message);
grinder.logger.info(hello.world[1].text())
grinder.logger.info(hello.world[1].'@a')
```

This will print "WOW2" and "wow_attr". Please refer the detailed [API usage](http://groovy.codehaus.org/Reading+XML+using+Groovy's+XmlParser)