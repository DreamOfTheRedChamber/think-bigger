# OpenText Analytics

<!-- MarkdownTOC -->

- [Background](#background)
	- [Product - BIRT](#product---birt)
		- [Overview](#overview)
			- [Design service](#design-service)
			- [Report service](#report-service)
			- [Data service](#data-service)
			- [Chart serivce](#chart-serivce)
	- [The most challenging part - How to dive into really large code base](#the-most-challenging-part---how-to-dive-into-really-large-code-base)
		- [Approaches you might consider](#approaches-you-might-consider)
		- [Some of the things I do to clarify code are](#some-of-the-things-i-do-to-clarify-code-are)
		- [As for the place to start?](#as-for-the-place-to-start)
	- [Features I implemented](#features-i-implemented)
		- [RLE on cache layer](#rle-on-cache-layer)
			- [Column-based](#column-based)
			- [Fact/Dictionary table](#factdictionary-table)
			- [Run length encoding](#run-length-encoding)
			- [Possible improvements](#possible-improvements)
		- [Implement integration test](#implement-integration-test)
			- [Components](#components)
			- [Whole process](#whole-process)
			- [How to add integration tests](#how-to-add-integration-tests)
			- [Sample pom.xml](#sample-pomxml)
		- [Sample java test file MainIT.java](#sample-java-test-file-mainitjava)
		- [How to run integration test](#how-to-run-integration-test)

<!-- /MarkdownTOC -->


# Background

## Product - BIRT
* My team works on business intelligence reporting tools. It is one of the top three open source BI vendors. On the technology side, we use Java, Maven, Highcharts and Tomcat. 

### Overview
* First, you can design your reports inside our report studio by dragging and dropping.
* Then, the report data connector will connect to different types of data sources and retrieve the data we need. 
* Next, all kinds of data transformation defined in the report such as filtering, grouping and slicing are applied to the data.
* After data is ready, different report items such as charts, cross tabs, pivot tables could be generated. 
* Finally, the in-memory report could be emitted in the desired output format such as Excel, PDF, HTML, Powerpoint or Word.

#### Design service
* To create a persistent report, the report engine transforms and summarizes the data and caches the generated report in an intermediate binary file, the report document file. This caching mechanism enables BIRT to scale to handle large quantities of data. 

#### Report service
* The BIRT report engine enables XML report designs created by teh BIRT report designer to be used by a J2EE/Java application. To support this functionality, the report engine provides two core services, generation and presentation. 

* Generation services: The generation service within the report engine connects to the data sources specified in a report design, uses the data engine to retreive and process data, creates the report layout, and generates the report document. Report document can be either viewed immediately using the presentation services, or saved for later use. 

* Presentation services: The presentation services process the report document created by the generation services and render the report to the requested format and the layout specified in the design. The presentation services use the data engine to retrieve and process data from the report document. The presentation services use whichever report emitter they require to generate a report in the requested format. BIRT has several standard emitters, HTML, PDF, DOC, PPT, PS and XLS. 

#### Data service
* The data engine contains the APIs and provides services to retrieve and transform data. The data services retrieve data from its source and process the data as specified by the report design. When used by the generation engine, the data services retrieve data from the data source specified in the design. When used by the presentation engine, the data services retrieve data from the report document. 
* The data engine provides two key service types: data access services and data transformation services. The data access services communicate with the ODA framework to retrieve data. The data transformation services perform operations such as sorting, grouping, aggregating, and filtering the data returned by the data access services.
* BIRT uses the ODA framework provided by the Eclipse Data Tools Platform project to manage ODA and native drivers, load drivers, open connections and manage data requests. 

#### Chart serivce
* Creates all types of charts.

## The most challenging part - How to dive into really large code base

### Approaches you might consider
1. Try to find out what the code is supposed to do, in business terms.
2. Read all the documentation that exists, no matter how bad it is.
3. Talk to anyone who might know something about the code.
4. Step through the code in the debugger.
5. Introduce small changes and see what breaks.
6. Make small changes to the code to make it clearer.

### Some of the things I do to clarify code are
1. Run a code prettifier to format the code nicely.
2. Add comments to explain what I think it might do
3. Change variable names to make them clearer (using a refactoring tool)
4. Using a tool that highlights all the uses of a particular symbol
5. Reducing clutter in the code - commented out code, meaningless comments, pointless variable initializations and so forth.
6. Change the code to use current code conventions (again using refactoring tools)
7. Start to extract functionality into meaningful routines
8. Start to add tests where possible (not often possible)
9. Get rid of magic numbers
10. Reducing duplication where possible

### As for the place to start? 
* Start with what you do know. I suggest inputs and outputs. You can often get a handle on what these are supposed to be and what they are used for. Follow data through the application and see where it goes and how it is changed.
* One of the problems I have with all this is motivation - it can be a real slog. It helps me to think of the whole business as a puzzle, and to celebrate the progress that I'm making, no matter how small.

## Features I implemented
### RLE on cache layer
* We attempt to reduce the size of the data cache layer so that BI reports could load them into memory faster. The data source could be thought of as a two dimensional table. And right now the problem is how we store this two dimensional table more efficiently. There are three levels of optimization altogether and I implemented the last part. 

#### Column-based
* The two dimensional table is stored in a column-oriented manner. There are two reasons for this:
  - First, row-oriented storage is great for write efficiency because it is really easy to add/modify a record. Column-oriented storage is great for read efficiency because we can read only columns which we are interested in. In addition, column-oriented storage usually has higher compression efficiency because similar values are grouped together. As a result, they usually have less entropy and are easier to compact and encode. 
  - Second, typically a business intelligence report data source contains hundreds of columns and millions of rows. A common use case for report users is they only use a few columns from hundreds of columns. In addition, when a business intelligence report runs, we want the data source being able to be loaded into the memory as fast as possible. This is exactly the use case of column-oriented storage.

#### Fact/Dictionary table
* There are usually a lot of repetitive values and the number of unique entries is far less than the total number of entries. So it is efficient to use a dictionary/fact table encoding. 

#### Run length encoding
* In business intelligence reports, columns appear as sorted keys and same values are grouped together. As a result, run-length encoding can be applied on these columns to further reduce the size of the report.

#### Possible improvements
* We implement encoding only on string values, not numeric values. Actually, if the numeric values exhibit great locality, a number of encoding algorithms could also be applied.

### Implement integration test
* Our reporting tools can be deployed into web applications as war files. We implemented integration tests to gaurantee that the generated war files are always correct. We use two Maven Failsafe to run integration tests. We use PhantomJS to run browerless testing and selenium for integration tests. 

#### Components 
- **PhantomJS**
	- **as a plugin**: phantomjs-maven-plugin for installing phantomjs to path ${phantomjs.binary}
	- **as a dependency**: phantomjsdriver used inside integration testing classes
- **Selenium**
	- **as a dependency**: selenium-java used inside integration testing classes
- **FailSafe**
	- **as a plugin**: Run integration testing with "*IT.java"
	- Rely on the {phantomjs.binary} variable setup previously
- **Embedded Jetty**
    - Run integration test on with war under ${project.build.directory}
    - HTTP port 9999

#### Whole process
1. Copy/Unpack the war to be run during package life-cycle.
2. Launch Jetty server during pre-integration-test life-cycle.
3. Fail-safe plugin run integration test with phantomJS during integration test life-cycle.
4. Jetty stop server during after-integration-test life-cycle.
5. Failsafe verify the tests run correctly during verify life-cycle.

#### How to add integration tests
- Add java tests ending with naming convention "*IT.java"

#### Sample pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>org.eclipse.birt</groupId>
	<artifactId>birt-integration-test</artifactId>
	<version>4.6.0-SNAPSHOT</version>
	<packaging>pom</packaging>
	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>3.8.1</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.seleniumhq.selenium</groupId>
			<artifactId>selenium-java</artifactId>
			<version>2.53.0</version>
		</dependency>
		<dependency>
			<groupId>com.github.detro</groupId>
			<artifactId>phantomjsdriver</artifactId>
			<version>1.2.0</version>
			<scope>test</scope>
		</dependency>
	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>com.github.klieber</groupId>
				<artifactId>phantomjs-maven-plugin</artifactId>
				<version>0.7</version>
				<executions>
					<execution>
						<goals>
							<goal>install</goal>
						</goals>
					</execution>
				</executions>
				<configuration>
					<version>1.9.0</version>
					<checkSystemPath>true</checkSystemPath>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.5.1</version>
				<executions>
					<execution>
						<phase>test-compile</phase>
						<goals>
							<goal>testCompile</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-failsafe-plugin</artifactId>
				<version>2.19.1</version>
				<configuration>
					<systemPropertyVariables>
						<phantomjs.binary>${phantomjs.binary}</phantomjs.binary>
					</systemPropertyVariables>
				</configuration>
				<executions>
					<execution>
						<id>integration-test</id>
						<phase>integration-test</phase>
						<goals>
							<goal>integration-test</goal>
						</goals>
						<configuration>
							<excludes>
								<exclude>none</exclude>
							</excludes>
							<includes>
								<include>**/*IT.java</include>
							</includes>
						</configuration>
					</execution>
					<execution>
						<id>verify</id>
						<goals>
							<goal>verify</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.eclipse.jetty</groupId>
				<artifactId>jetty-maven-plugin</artifactId>
				<version>9.3.8.v20160314</version>
				<configuration>
					<war>${project.basedir}/birt.war</war> 
					<scanIntervalSeconds>10</scanIntervalSeconds>
					<stopKey>foo</stopKey>
					<stopPort>8090</stopPort>
					<httpConnector>
						<port>9999</port>
					</httpConnector>
				</configuration>
				<executions>
					<execution>
						<id>start-jetty</id>
						<phase>pre-integration-test</phase>
						<goals>
							<goal>deploy-war</goal>
						</goals>
						<configuration>
							<daemon>true</daemon>
							<reload>manual</reload>
						</configuration>
					</execution>
					<execution>
						<id>jetty-stop</id>
						<goals>
							<goal>stop</goal>
						</goals>
						<phase>post-integration-test</phase>
					</execution>
				</executions>
				<dependencies/>
			</plugin>
 		</plugins>
	</build>
</project>
```

### Sample java test file MainIT.java
```java
import junit.framework.TestCase;
import static junit.framework.Assert.assertEquals;
import static junit.framework.Assert.assertTrue;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.phantomjs.PhantomJSDriver;
import org.openqa.selenium.support.ui.ExpectedCondition;
import org.openqa.selenium.support.ui.WebDriverWait;


public class MainIT extends TestCase {
    public void testExecute()
    throws Exception
    {
        System.setProperty("phantomjs.binary.path", System.getProperty("phantomjs.binary"));
        WebDriver driver = new PhantomJSDriver();
        
        // And now use this to visit Google
        driver.get("http://localhost:9999");

        // // Check the title of the page
        assertEquals("Eclipse BIRT Home", driver.getTitle());
        driver.findElement(By.linkText("View Example")).click();

        //Close the browser
        driver.quit();    
    }

}
```

### How to run integration test
```bash
mvn verify
```

