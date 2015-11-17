nGrinder 3.1.1 contains json.jar, one of most famous Json parser(provided from www.json.org). You can easily parse json string with this library.  
You can use this like the following.

##### JYTHON
```
from org.json import JSONObject
 
message = """
{
  "glossary": {
     "title": "example glossary",
        "GlossDiv": {
           "title": "S",
           "GlossList": {
              "GlossEntry": {
                 "ID": "SGML",
                 "SortAs": "SGML",
                 "GlossTerm": "Standard Generalized Markup Language",
                 "Acronym": "SGML",
                 "Abbrev": "ISO 8879:1986",
                 "GlossDef": {
                    "para": "A meta-markup language, used to create markup languages such as DocBook."
                 },
                 "GlossSee": "markup"
            }
         }
      }
  }
}
"""
 
json = JSONObject(message)
grinder.logger.info(json.get("glossary").getString("title"))
```

##### GROOVY
For groovy you can use JsonSlurper instead.
```
import groovy.json.JsonSlurper
 
 
....
def message = """
{
  "glossary": {
     "title": "example glossary",
        "GlossDiv": {
           "title": "S",
           "GlossList": {
              "GlossEntry": {
                 "ID": "SGML",
                 "SortAs": "SGML",
                 "GlossTerm": "Standard Generalized Markup Language",
                 "Acronym": "SGML",
                 "Abbrev": "ISO 8879:1986",
                 "GlossDef": {
                    "para": "A meta-markup language, used to create markup languages such as DocBook."
                 },
                 "GlossSee": "markup"
            }
         }
      }
  }
}"""
def jsonMsg = new JsonSlurper().parseText(message)
grinder.logger.info(jsonMsg.glossary.title)
```