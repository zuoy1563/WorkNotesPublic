# XML Processing

Topics:
- XML Basics
- XML schema
- XQuery
- XPath

# XML Basics
## XML structure

```
<?xml version="1.0" encoding="UTF-8"?>
<game_store>
    <video_games xmlns:v="http://www.w3.org/TR/html4/">
        <game id="1">
            <name>Assassin's Creed</name>
            <directors>
                <director>
                    <id>123</id>
                    <name>Jonathan Dumont</name>
                </director>
                <director>
                    <id>124</id>
                    <name>Scott Phillips</name>
                </director>
            </directors>
            <price>79</price>
        </game>
        <game id="2">
            <name>Resident Evil 2 Remake</name>
            <directors>
                <director>
                    <id>233</id>
                    <name>Kazunori Kadoi</name>
                </director>
                <director>
                    <id>234</id>
                    <name>Yasuhiro Anpo</name>
                </director>
            </directors>
            <price>99</price>
        </game>
    </video_games>
    <online_games>
        <game>
            <id>3</id>
            <name>GranBlue Fantasy</name>
            <directors>
                <director>
                    <id>555</id>
                    <name>KMR</name>
                </director>
            </directors>
        </game>
    </online_games>
</game_store>
```
**Note**: their should be a root element like ```<gamestore>``` to wrap other elements.

## XML Namespaces
In the previous example, if there were two different types of games and they have different elements and attributes, they cannot both be named as "games". To avoid conflict, use ```<v:games>``` and ```<o:games>``` instead to help distinguish them.

Have a look at the following webpage for further information.
>http://www.xmlmaster.org/en/article/d01/c10/

# XML Schema (XSD)
Tutorial:
>https://www.liquid-technologies.com/xml-schema-tutorial/xsd-elements-attributes

XML to XSD generator:
>https://www.freeformatter.com/xsd-generator.html

XSD resctrictions:
>https://www.w3schools.com/xml/schema_facets.asp

Tips if you write by yourself:
- xs:sequence is a must before any xs:element in xs:complexType
- xs:attribute must appear after xs:sequence
- minOccurs and maxOccurs are both default to 1

**xs: Data Types**
- xs:string
- xs:decimal
- xs:integer
- xs:boolean
- xs:date
- xs:time

# XPath, XQuery and Saxon
XQuery Functions:
>http://www.xqueryfunctions.com/xq/

XQuery tutorial:
>https://www.tutorialspoint.com/xquery/index.htm
>http://edutechwiki.unige.ch/en/XQuery_tutorial_-_basics

XQuery should be put in .xq files separately, just like SQL scripts.
XPath are used altogether with XQuery.
Please see games.xq for example.

XQuery Tips:
- function body is enclosed by {}. It must end with a return statement
- variable value is also enclosed by {}
- use //NODE_NAME to avoid using for loops


Java side will use Saxon to run XQuery.

```
    @Test
    private void executeXQuery(String filename, String externalVariable) throws Exception {
        Processor saxon = new Processor(false);
        XQueryCompiler compiler = saxon.newXQueryCompiler();
        XQueryExecutable exec = compiler.compile(new File(filename));
        XQueryEvaluator query = exec.load();
        StringWriter sw = new StringWriter();
        if (!externalVariable.equals(""))
            query.setExternalVariable(new QName("keyword"), new XdmAtomicValue(externalVariable));
        query.run(saxon.newSerializer(sw));
        String result = sw.toString();
        System.out.println(result);
    }
```

# XSLT and Saxon
XSLT tutorial:
>https://www.w3schools.com/xml/xsl_intro.asp

Java side function call:

```
    // for generating xml or html using saxon 9.9
    private void generateDocumentByXSL(String type) throws Exception {
        type = type.toLowerCase();
        assert type.equals("html") || type.equals("xml");
        Processor processor = new Processor(false);
        XsltCompiler compiler = processor.newXsltCompiler();
        XsltExecutable stylesheet = compiler.compile(new StreamSource(new File("XSLT_FILE_PATH.xsl")));
        Serializer out = processor.newSerializer(new File("output/OUTPUT_FILE_NAME." + type));
        out.setOutputProperty(Serializer.Property.METHOD, type);
        out.setOutputProperty(Serializer.Property.INDENT, "yes");
        Xslt30Transformer transformer = stylesheet.load30();
        transformer.transform(new StreamSource(new File("INPUT_XML_FILE_PATH.xml")), out);
    }

```

# JAXB
Maven plugin for generating pojo classes according to .xsd files
```
    ...
    <build>
        <plugins>
            <plugin>
                <groupId>org.jvnet.jaxb2.maven2</groupId>
                <artifactId>maven-jaxb2-plugin</artifactId>
                <version>0.14.0</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
    ...
```
You can also write pojo classes yourself.
Tips:
- The root element should have its own class even if it is a collection like ```orders```
- Every entity (or xml element) should be annotated with ```@XmlRootElement```
- ```@XmlAccessorType(XmlAccessType.FIELD)```
- ```@XmlElement(name = "user_id", required = true)```
- If there is a list attribute inside an entity (like ```products``` in ```order```), give ```@XmlElementWrapper(name = "products")``` and
    ```@XmlElement(name = "product", required = true)``` over ```private List<Product> products;```
- Override euqals and hashCode if necessary

## Marshal & Unmarshal
Marshal: transform an object into xml

Java side code:
```
    private void marshal(String schemaName, Object object) throws Exception {
        JAXBContext jaxbContext = JAXBContext.newInstance(object.getClass());
        Marshaller jaxbMarshaller = jaxbContext.createMarshaller();

        SchemaFactory sf = SchemaFactory.newInstance(XMLConstants.W3C_XML_SCHEMA_NS_URI);
        Schema schema = sf.newSchema(new File(SCHEMA_PATH));

        // output pretty printed
        jaxbMarshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
        // check against schema
        jaxbMarshaller.setSchema(schema);

        jaxbMarshaller.marshal(object, System.out);
    }
```

Unmarshal: transform xml into an objct

Java side code:
```
    private Object unmarshal(String schemaName, String xmlName, Class targetClass) throws Exception {
        SchemaFactory sf = SchemaFactory.newInstance(XMLConstants.W3C_XML_SCHEMA_NS_URI);
        Schema schema = sf.newSchema(new File(SCHEMA_PATH));

        JAXBContext jc = JAXBContext.newInstance(targetClass); 

        Unmarshaller unmarshaller = jc.createUnmarshaller();
        // check against schema
        unmarshaller.setSchema(schema);
        // user defined validation handler
        //unmarshaller.setEventHandler(new MyValidationEventHandler());
        return unmarshaller.unmarshal(new File(fileBase + xmlName));
    }
```

## Validation
As you can see above, you can check the styles against .xsd file and define the validation hanlder yourself

See full tutorial here:
>http://blog.bdoughan.com/2010/12/jaxb-and-marshalunmarshal-schema.html
