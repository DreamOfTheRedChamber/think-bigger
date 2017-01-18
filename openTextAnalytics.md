# OpenText Analytics
# Background
## Company


## Team


## Product
### Overview
* BIRT report designer provides drag-and-drop capabilities to design reports quickly. The report designer uses the design engine to produce XML report design files. The BIRT report engine consumes these report design files, then, at run-time, fetches the appropriate data using queries defined at design time. A BIRT report for immediate viewing is generated in memory and emitted in the desired output format, which can be a Microsoft Excel, non-paginated HTML, paginated HTML, PDF, Powerpoint, PostScript or Word file. 

#### Design engine
* To create a persistent report, the report engine transforms and summarizes the data and caches the generated report in an intermediate binary file, the report document file. This caching mechanism enables BIRT to scale to handle large quantities of data. 

#### Report engine
* The BIRT report engine enables XML report designs created by teh BIRT report designer to be used by a J2EE/Java application. To support this functionality, the report engine provides two core services, generation and presentation. 

* Generation services: The generation service within the report engine connects to the data sources specified in a report design, uses the data engine to retreive and process data, creates the report layout, and generates the report document. Report document can be either viewed immediately using the presentation services, or saved for later use. 

* Presentation services: The presentation services process the report document created by the generation services and render the report to the requested format and the layout specified in the design. The presentation services use the data engine to retrieve and process data from the report document. The presentation services use whichever report emitter they require to generate a report in the requested format. BIRT has several standard emitters, HTML, PDF, DOC, PPT, PS and XLS. 

#### Data engine
* The data engine contains the APIs and provides services to retrieve and transform data. The data services retrieve data from its source and process the data as specified by the report design. When used by the generation engine, the data services retrieve data from the data source specified in the design. When used by the presentation engine, the data services retrieve data from the report document. 
* The data engine provides two key service types: data access services and data transformation services. The data access services communicate with the ODA framework to retrieve data. The data transformation services perform operations such as sorting, grouping, aggregating, and filtering the data returned by the data access services.
* BIRT uses the ODA framework provided by the Eclipse Data Tools Platform project to manage ODA and native drivers, load drivers, open connections and manage data requests. 

#### Chart engine
* Creates all types of charts.

# My Involvement
## RLE on cache layer
* We attempt to reduce the size of the data cache layer so that BI reports could load them into memory faster. The data source could be thought of as a two dimensional table. And right now the problem is how we store this two dimensional table more efficiently. 

### Column-based
* The two dimensional table is stored in a column-oriented manner. There are two reasons for this:
  - First, row-oriented storage is great for write efficiency because it is really easy to add/modify a record. Column-oriented storage is great for read efficiency because we can read only columns which we are interested in. In addition, column-oriented storage usually has higher compression efficiency because similar values are grouped together. As a result, they usually have less entropy and are easier to compact and encode. 
  - Second, typically a business intelligence report data source contains hundreds of columns and millions of rows. A common use case for report users is they only use a few columns from hundreds of columns. In addition, when a business intelligence report runs, we want the data source being able to be loaded into the memory as fast as possible. This is exactly the use case of column-oriented storage.

### Fact/Dictionary table
* There are usually a lot of repetitive values and the number of unique entries is far less than the total number of entries. So it is efficient to use a dictionary/fact table encoding. 

### Run length encoding
* In business intelligence reports, columns appear as sorted keys. As a result, run-length encoding can be applied on these columns.

## The most challenging part
* Our 6team is responsible 

## Possible improvements
### Encoding algorithms
