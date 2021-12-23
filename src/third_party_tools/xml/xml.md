- [XML Tutorial](#xml-tutorial)
  * [XML Syntax Rules](#xml-syntax-rules)
  * [Characters and Encoding](#characters-and-encoding)
  * [Element](#element)
  * [Attribute](#attribute)
  * [XML Elements vs. Attributes](#xml-elements-vs-attributes)
  * [XML Prefix and Namespaces](#xml-prefix-and-namespaces)
  * [CDATA](#cdata)
- [XML DTD](#xml-dtd)
- [XML XSD](#xml-xsd)
  * [XSD Data Types](#xsd-data-types)
    + [String](#string)
    + [Date/ Time/ DateTime/Duration Data Type](#date--time--datetime-duration-data-type)
      - [Date](#date)
      - [Time](#time)
      - [DateTime](#datetime)
      - [Duration](#duration)
    + [Numeric Data](#numeric-data)
      - [Decimal](#decimal)
      - [Integer](#integer)
    + [XSD Simple Elements](#xsd-simple-elements)
      - [Default and Fixed Values for Elements](#default-and-fixed-values-for-elements)
    + [XSD Attributes](#xsd-attributes)
      - [Default and Fixed Values for Attributes](#default-and-fixed-values-for-attributes)
      - [Optional and Required Attributes](#optional-and-required-attributes)
    + [XSD Complex Element](#xsd-complex-element)
    + [XSD Schema](#xsd-schema)
- [XML Databases](#xml-databases)
  * [XPath](#xpath)
    + [XPath Path Expressions](#xpath-path-expressions)
    + [XPath Functions](#xpath-functions)
    + [XPath Operators](#xpath-operators)
    + [XPath Axes](#xpath-axes)
  * [XQuery](#xquery)
- [XSLT](#xslt)

# XML Tutorial

XML is the abbreviation for eXtensible Markup Language and it has been designed with the purpose of storing and transporting data. XML was created with the goal of being both human and machine readable. XML is just information wrapped in tags. XML, unlike HTML does not use predefined tags.
```
<?xml version="1.0" encoding="UTF-8"?>
<book>
	<title lang="fr">UML Distilled</title>
	<ISBN>789654 </ISBN>
	<author>Martin Fowler </author>
	<author>Kendall Scott </author>
</book>
```
XML is extensible, that means you can add the followings:

```
<?xml version="1.0" encoding="UTF-8"?>
<book>
	<title lang="fr">UML Distilled</title>
	<ISBN>789654 </ISBN>
	<author>Martin Fowler </author>
	<author>Kendall Scott </author>
	<price>19.0</price>
</book>
```
## XML Syntax Rules
An XML document begins with a root element and branches to child elements from there. child elements are allowed to have sub-child elements as well.
XML documents must contain one root element which is the parent of all other elements:
```
<root>
  <child>
    <subchild>.....</subchild>
  </child>
</root>
```
## Characters and Encoding
Almost every legal Unicode character may appear in an XML document, i.e. `ø,æ,å,ê,è,é`. XML document is **case-sensitive**.
Escaping characters begin with the character `&` and end with a `;`

- `&lt;` represents "<";
- `&gt;` represents ">";
- `&amp;` represents "&";
- `&apos;` represents "'";
- `&quot;` represents '"'.
White spaces are preserved in XML.

To declare XML document you should add the following line (**Prolog**) to the top of your document (optional, but if it exists, it must come as the first line in the document):
`<?xml version = "version_number" encoding = "encoding_declaration" standalone = "standalone_status" ?>`

- Version:	`1.0,  1.1`
- Encoding:	`UTF-8, UTF-16, ISO-10646-UCS-2, ISO-10646-UCS-4, ISO-8859-1 to ISO-8859-9, ISO-2022-JP, Shift_JIS, EUC-JP`
- Standalone:		`yes` or `no`




## Element
An element is a logical document component that either begins with a start-tag and ends with a matching end-tag

```
<element-name attributes="value"> contents</element-name>
```
or consists only of an empty-element tag:
```
<element-name attributes="value" /> 
```
An element can contain:
- other elements.
- text.
- attributes.
- a mixture of the above (including none of them).

```
<mediatech>
  <movie category="sci-fi">
    <title>The Matrix Resurrections</title>
    <director>Lana Wachowski</director>
    <year>2021 </year>
    <length>2h 28m</length>
  </movie>
  <book category="IT">
    <title>UML distilled</title>
    <author>Martin Fowler</author>
    <year>1997</year>
    <price>39.95</price>
  </book>
</mediatech>
```

In the example above:

- `<title>, <director>, <author>, <length>, <year>`, and `<price>` have text content because they contain text (like 39.95).

- `<mediatech>, <movie>` and `<book>` have element contents, because they contain elements.

- `<book> and <movie>` have  attribute (category="sci-fi").


## Attribute
An attribute is a markup construct consisting of a name-value pair that exists within a tag:

`<movie category="sci-fi">`

Attributes can **not** contain tree structures and they are not easily expandable (for future changes)
Attribute values must always be quoted. Either single or double quotes can be used.



## XML Elements vs. Attributes
There are no rules about when to use attributes or when to use elements in XML.

Elements form:
```
<movie>
	<category>sci-fi<category>
	<title>The Matrix Resurrections</title>
	<director>Lana Wachowski</director>
	<year>2021 </year>
	<length>2h 28m</length>
</movie>
```

Attribute form:
```
<movie category="sci-fi">
	<title>The Matrix Resurrections</title>
	<director>Lana Wachowski</director>
	<year>2021 </year>
	<length>2h 28m</length>
</movie>
```

## XML Prefix and Namespaces
suppose you have the followings XML fragments:

```
<library>
    <name>OpenCV</name>
    <language>C++</language>
</library>
```
and 

```
<library>
    <name>City Media-tech Library</name>
    <type>public</type>
</library>
```

If these were added together, there would be a name conflict and a user or an XML parser will not be able to handle these differences. This problem can be solved by using a prefix.


```
<root>
<s:library>
    <s:name>OpenCV</s:name>
    <s:language>C++</s:language>
</s:library>

<p:library>
    <p:name>City Media-tech Library</p:name>
    <p:type>public</p:type>
</p:library>
</root>
```
If you use prefixes in XML, a namespace for the prefix must be defined. The namespace can be defined by an `xmlns` attribute in the start tag of an element with the following syntax. `xmlns:prefix="URI"`. An URI (Uniform Resource Identifier) is a string of characters which identifies an Internet Resource. URL is the most common URI which identifies an Internet domain address. 

```
<root>

<s:library xmlns:s="http://exmple.com/software-libraries/">
    <s:name>OpenCV</s:name>
    <s:language>C++</s:language>
</s:library>

<p:library xmlns:p="http://exmple.com/physical-libraries" >
    <p:name>City Media-tech Library</p:name>
    <p:type>public</p:type>
</p:library>

</root>
```
The namespace URI is **not** used by the parser to look up information. The use of a URI serves to give the namespace a unique name. Companies, frequently employ the namespace as a link to a web page containing namespace information. 



## CDATA 
Stands for Character Data and has the following form:
```cpp
<![CDATA[  your-string  ]]>
```
and it means that the data in between `<![CDATA[` and `]]>` includes data that could be interpreted as XML markup, but should not be. 
Any character data (other than `]]>`) can appear within the section without needing to be escaped. i.e. angle brackets `<>` and ampersands `&`.

For instance imagine you have a tutorial page in which your data includes lots of characters that include `&` and `<` but those characters aren't meant to be xml.
Instead of writing it like this:
```
<example-code>
while (x &lt; len &amp;&amp; !done) {
    print( &quot;Still working, &apos;zzz&apos;.&quot; );
    ++x;
    }
</example-code>
```
 you can write it as:
 
```cpp
<example-code><![CDATA[
while (x < len && !done) {
    print( "Still working, 'zzzz'." );
    ++x;
    }
]]></example-code>
```

The key differences between CDATA and comments is CDATA is **still part of the document, while a comment is not**.


# XML DTD
XML DTD (Document Type Definition) defines what elements are required and what attributes can be set. "Well Formed" XML refers to an XML document having correct syntax. 
A DTD-validated XML document is both "Well Formed" and "Valid."
DTDs check vocabulary and validity of the structure of XML documents against grammatical rules of appropriate XML language.
An XML DTD can be either specified inside the document, or outside.

Example of internal DTD

```
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<!DOCTYPE address
[
    <!ELEMENT address (name,company,phone)>
    <!ELEMENT name (#PCDATA)>
    <!ELEMENT company (#PCDATA)>
    <!ELEMENT phone (#PCDATA)>
]
>
<address>
    <name>customer name</name>
    <company>co ltd</company>
    <phone>123456</phone>
</address>
```

Example of external DTD
```
<?xml version = "1.0" encoding = "UTF-8" standalone = "no" ?>
<!DOCTYPE address SYSTEM "address.dtd">
<address>
   <name>Tanmay Patil</name>
   <company>TutorialsPoint</company>
   <phone>(011) 123-4567</phone>
</address>
```

The file `address.dtd` contains:
```
<!ELEMENT address (name,company,phone)>
<!ELEMENT name (#PCDATA)>
<!ELEMENT company (#PCDATA)>
<!ELEMENT phone (#PCDATA)>

```
PCDATA means parse-able text data.

# XML XSD
XML XSD (Schema Definition) is also used to describe and validate the structure and the content of XML data.
XSD dictates what elements and attributes should appear in a document, number and order of them, data types and also default and fixed values for them. 
The fundamental distinction between DTDs and XML Schema is that XML Schema uses an XML-based syntax, whereas DTDs use a unique syntax that dates back to SGML DTDs. Although DTDs are frequently chastised for requiring users to learn a new grammar, the syntax is actually extremely simple. The converse is true for XML Schema, which is verbose but also uses tags and XML, making the syntax of XML Schema less scary.



## XSD Data Types

The following is the list of most common XSD data types
### String 
```
<xs:element name="elementName" type="xs:string"/>
```
to trim spaces, line feeds, carriage returns, tabs use `xs:token`
```
<xs:element name="elementName" type="xs:token"/>
```
[List of all XSD string types](https://www.w3schools.com/xml/schema_dtypes_string.asp)

### Date/ Time/ DateTime/Duration Data Type
All field are mandatory.

#### Date
```
<xs:element name="startTime" type="xs:time"/>
<startDate>YYYY-MM-DD</startDate>
```
#### Time
```
<xs:element name="startDate" type="xs:date"/>
<startTime>hh:mm:ss</startTime>
```
#### DateTime

```
<xs:element name="startDateTime" type="xs:dateTime"/>
<startDateTime>YYYY-MM-DDThh:mm:ss</startDateTime>
```
#### Duration

```
<xs:element name="period" type="xs:duration"/>
<period>P2Y4M7DT1H</period>
```

Period of 2 years, 4 months, 7 days, and 1 hours.

[List of all XSD  date time data types](https://www.w3schools.com/xml/schema_dtypes_date.asp)


### Numeric Data
#### Decimal
```
<xs:element name="size" type="xs:decimal"/>
<size>69.420</size>
```
#### Integer
```
<xs:element name="id" type="xs:integer"/>
<id>10</id>
```



[List of all XSD numeric types](https://www.w3schools.com/xml/schema_dtypes_numeric.asp)



[List of all built in primitive datatypes](https://www.w3.org/TR/xmlschema11-2/#built-in-primitive-datatypes)



### XSD Simple Elements
An XML element that just hold text is a simple element. There can't be any additional elements or attributes in it.

Here are some simple XML elements:

```
<title>On the Origin of Species</title>
<ISBN>987123 </ISBN>
<author>Charles Darwin</author>
<price>42.0</price>
```

The corresponding simple element definitions:

```
<?xml version="1.0"?>

<xs:schema>
<xs:element name="title" type="xs:string" />
<xs:element name="ISBN" type="xs:integer" />
<xs:element name="author" type="xs:string"/>
<xs:element name="price" type="xs:decimal" />
</xs:schema>
```

#### Default and Fixed Values for Elements
When no additional value is specified for an element, it is given a default value.
```
<xs:element name="year" type="xs:date" default="2020-01-01"/>
```

A fixed value is also automatically assigned to the element, and you cannot specify another value.

```
<xs:element name="year" type="xs:date" fixed="2020-01-01"/>
```



### XSD Attributes

The syntax for defining an attribute is:

```
<xs:attribute name = "attributeName" type = "xxxxxx"/>
```
Here is an XML element with an attribute:

```
<title lang="en">The lord of the ring</title>
```
The corresponding attributes definitions:
```
<xs:attribute name="lang" type="xs:string"/>
```

#### Default and Fixed Values for Attributes
Just like elements, when no additional value is specified, it is given a default value.
```
<xs:attribute name="lang" type="xs:string" default="EN"/>
<xs:attribute name="lang" type="xs:string" fixed="EN"/>
```
#### Optional and Required Attributes
By default, attributes are optional. Use the "use" to indicate that the attribute is required:
```
<xs:attribute name="lang" type="xs:string" use="required"/>
```


### XSD Complex Element
An XML element that contains other elements and/or attributes is known as a complicated type element.

There are four kinds of complex elements:

1) empty elements.
```
<book ISBN="458184"/>
```
2) elements that containing only other elements.
```
<book>
<ISBN> 123654</ISBN>
<author>J. R. R. Tolkien</author>
<price>69.69</price>
</book>
```
3) elements that containing only text.
```
<title lang="en">The lord of the ring</title>
```
4) elements that containing both other elements and text.
```
<event>
occured on <date lang="en">01.01.2020</date>
</event>
```
Any of above element may or may not contain attributes as well.

### XSD Schema
The `<schema>` element is the root element of every XML Schema:

```
<?xml version="1.0"?>

<xs:schema>
.
.
.
</xs:schema>
```

Referencing a Schema in an XML Document: 

```
<?xml version="1.0"?>

<movie xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://www.example.com/movie.xsd">
    <title>The Matrix Resurrections</title>
    <director>Lana Wachowski</director>
    <year>2021-10-01 </year>
    <length>2h 28m</length>
</movie>
```


Refs: [1](https://stackoverflow.com/questions/1544200/what-is-difference-between-xml-schema-and-dtd)
[Online XSD Validator](https://www.utilities-online.info/xsdvalidation)
[Online XSD Generator](https://www.freeformatter.com/xsd-generator.html#ad-output)

# XML Databases
Information in the XML format could be stored in an XML database. 
<!-- -->
There are two major types of XML databases:

1) XML- enabled
2) Native XML (NXD)

Example of XML database:

```
<?xml version="1.0" encoding="UTF-8" ?>
<?xml-stylesheet type="text/xsl" href="book.xsl" ?>
<shop>
	<!-- Comment: This is a list of books -->
	<book>
		<title lang="en">The lord of the ring</title>
		<ISBN> 123654</ISBN>
		<author>J. R. R. Tolkien</author>
		<price>69.69</price>
	</book>
	<book>
		<title>On the Origin of Species</title>
		<ISBN>987123 </ISBN>
		<author>Charles Darwin</author>
		<price>42.0</price>
	</book>
	<book>
		<title lang="fr">UML Distilled</title>
		<ISBN>789654 </ISBN>
		<author>Martin Fowler </author>
		<author>Kendall Scott </author>
		<price>19.0</price>
	</book>
</shop>	
```
## XPath
XPath is made of a path like syntax similar to directory structures, `/, //, ., .., etc.` to select nodes or node-sets in an XML document. 
It is a language for navigating in XML documents. 
<!-- In XPath, there are seven kinds of nodes: element, attribute, text, namespace, processing-instruction, comment, and document nodes.
-->



### XPath Path Expressions

All books from root:
```
/
```

Selects all titles in the document from the root:
```
/title
```

Selects all titles in the document from the current node, no matter where they are:
```
//title
```

Which is equal to:
```
/shop/book/title
```

Select the parent of title which is book
```
/shop/book/title/..
```

First title
```
/shop/book/title[1]
```


All books that have a language attribute:
```
/shop/book/title[@lang]
```
All book with french language: 
```
//title[@lang='fr']
```
All languages:
```
//@lang
```
or
```
/shop/book/title/@*
```


### XPath Functions
Last title:

```
/shop/book/title[last()]
```

Count numbers of all books:
```
count(/shop/book)
```

[List of XPath Functions](https://developer.mozilla.org/en-US/docs/Web/XPath/Functions)


### XPath Operators
You can use operations on the Xpath. 

The price of the book with the title `On the Origin of Species`:
```
/shop/book[title='On the Origin of Species']/price
```


Title of all book with the price over 40.0:
```
/shop/book[price>40.0]/title
```
All titles with prices:

```
/shop/book/title | /shop/book/price
```
[List of XPath Operators](https://www.javatpoint.com/xpath-operators)

### XPath Axes
In Xpath you use location path to define location of a node using absolute or relative path. You can also use **axes** to identify elements by their relationship like
**parent**, **child**, **siblings**, **ancestors** (a node's parent, parent's parent,...), **descendants** (node's children, children's children,...)

```
/shop/book/preceding-sibling::comment()
```

```
/shop/book/following-sibling::node()
```
```
/shop/book[3]/preceding-sibling::node()
```

```
/shop/book[1]/following-sibling::node()
```
```
/descendant::price
```

```
/shop/child::book
```


[List of XPath Axes](https://www.w3schools.com/xml/xpath_axes.asp)  
[Online Xpath and XQuery Tester](https://www.videlibri.de/cgi-bin/xidelcgi)

Refs: [1](https://www.softwaretestinghelp.com/xpath-axes-tutorial/)


## XQuery
The data in the XML database can be queried, and exported in a variety of formats using **XQuery**. XQuery is to XML is like SQL is to databases. 
XQuery is similar to SQL query. You can use **FLWOR Expressions** for writing queries.

- For - selects a sequence of nodes
- Let - binds a sequence to a variable
- Where - filters the nodes
- Order by - sorts the nodes
- Return - what to return (gets evaluated once for every node)


consider the following XPath:
```
/shop/book[title='On the Origin of Species']/price
```
This can be expressed with XQuery as followings:

```
for $x in /shop/book
where $x/title='On the Origin of Species'
return $x/price
```

# XSLT 
XSLT (XML EXtensible Stylesheet Language Transformations) is a language for transforming XML documents into other XML documents/ formats such as HTML, plain text or XSL Formatting Objects, which may subsequently be converted to other formats, such as PDF, PostScript and PNG.
XSLT utilized XPath to find information in an XML document.
`<xsl:stylesheet>` or `<xsl:transform>` is the root element that declares the document to be an XSL style sheet. Either can be used (they are completely synonymous and can be used interchangeably). 

```
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
```
or 
```
<xsl:transform version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
```

For our XML file from previous example

```
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
<xsl:template match="/">
<html>
<body>
<table>
<tr>
	<td>title</td>
	<td>author</td>
	<td>ISBN</td>
</tr>
<xsl:for-each select="shop/book">
<tr>
  	<td> <xsl:value-of select="title"/> </td>
	<td> <xsl:value-of select="author"/> </td>
	<td> <xsl:value-of select="ISBN"/> </td>
</tr>
</xsl:for-each>
</table>
</body>
</html>
</xsl:template>
</xsl:stylesheet>
```
The transformed XML is:

```
<html>
   <body>
      <table>
         <tr>
            <td>title</td>
            <td>author</td>
            <td>ISBN</td>
         </tr>
         <tr>
            <td>The lord of the ring</td>
            <td>J. R. R. Tolkien</td>
            <td> 123654</td>
         </tr>
         <tr>
            <td>On the Origin of Species</td>
            <td>Charles Darwin</td>
            <td>987123 </td>
         </tr>
         <tr>
            <td>UML Distilled</td>
            <td>Martin Fowler </td>
            <td>789654 </td>
         </tr>
      </table>
   </body>
</html>
```

[Online XSL Transformer](https://www.freeformatter.com/xsl-transformer.html#ad-output) 
