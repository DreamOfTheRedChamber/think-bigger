# OpenText Analytics

<!-- MarkdownTOC -->

## Product - BIRT
* My team works on business intelligence reporting tools. It has an open source version https://projects.eclipse.org/projects/technology.birt and a commercialized version
* On the technology side, we use Java, Maven, Highcharts and Tomcat. 

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
