### Java
- Code written for an nGrinder 3.X project should follow the "Code Conventions for the Java Programming Language"
    - http://www.oracle.com/technetwork/java/codeconv-138413.html
- There are not many differences from Eclipse default java style. Just use the Eclipse default type.

### Exceptions
- 3.1.1 Beginning Comments
    - All source files should begin with a c-style comment that lists the class name, version information, date, and copyright notice:
```
/*
 * Copyright (C) 2012 - 2012 NHN Corporation
 * All rights reserved.
 *
 * This file is part of The nGrinder software distribution. Refer to
 * the file LICENSE which is part of The nGrinder distribution for
 * licensing details. The nGrinder distribution is available on the
 * Internet at http://nhnopensource.org/ngrinder
 *
 * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
 * "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
 * LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
 * FOR A PARTICULAR PURPOSE, ARE HERE BY DISCLAIMED. IN NO EVENT SHALL THE
 * COPYRIGHT HOLDERS OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
 * INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
 * (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
 * SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
 * HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT,
 * STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
 * ARISING IN ANY WAY FROM THE USE OF THIS SOFTWARE, EVEN IF ADVISED
 * OF THE POSSIBILITY OF SUCH DAMAGE.
 */
```

- Comment
    - Provide a class comment like the following. You should provide a simple description, author, and the version from which this class is provided.

```java
/**
 * Hibernate CUBRID Dialect
 *
 * @author JunHo Yoon
 * @since 3.0
 */
public class CUBRIDDialect extends Dialect {
....
}
```

- Line Length
    - Avoid lines longer than 120 characters, as they are not handled well by many terminals and tools.
- Packages
    - All ngrinder code is located under com.nhncorp.ngrinder package.
    - Each big category of features is divided into subpackage in the com.nhncorp.ngrinder.
- Do not use SQL directly, as this can cause DB incompatibilities.
- Eclipse Coding Style file : Eclipse_nGrinder.xml
    - You can import this style in Windows ==> Preferences ==> Java ==> Formatter

### JavaScript
- nGrinder project follows jQuery Coding Convention
    - http://docs.jquery.com/JQuery_Core_Style_Guidelines
- Summary
    - Use camel case for variables and methods
    - Indent code with space
    - Long comment should be maeked /* ... */
    - Strict equality checks (===) should be used preferentially to == wherever possible

### HTML/JSP
- Use underscore separated naming schemes for css, id and class.
- Avoid lines longer than 120 characters, as they are not handled well by many terminals and tools.

### XML
- Avoid lines longer than 120 characters, as they are not handled well by many terminals and tools.
