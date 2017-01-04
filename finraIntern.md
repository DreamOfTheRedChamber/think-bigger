# Finra intern project - Permissions Compliance System on AWS
<!-- MarkdownTOC -->

- [Related Presentations](#related-presentations)
- [Background](#background)
	- [FINRA](#finra)
		- [Def](#def)
		- [Company Missions](#company-missions)
		- [Company Visions](#company-visions)
		- [Finra and AWS](#finra-and-aws)
	- [Team](#team)
	- [Project](#project)
- [Technical components](#technical-components)
	- [IAM](#iam)
		- [Permission types](#permission-types)
			- [Users and groups](#users-and-groups)
			- [Roles](#roles)
		- [Policies](#policies)
			- [Structure](#structure)
			- [Evaluation](#evaluation)
	- [S3](#s3)
		- [Comparisons](#comparisons)
		- [How is benchmarks designed](#how-is-benchmarks-designed)
	- [CloudTrail](#cloudtrail)
		- [Def](#def-1)
		- [Use cases](#use-cases)
		- [Workflow](#workflow)
		- [Methods](#methods)
		- [Log example](#log-example)
			- [Information in a recorded API call](#information-in-a-recorded-api-call)
	- [CloudTrail + Splunk](#cloudtrail--splunk)
		- [Responsibilities:](#responsibilities)
	- [Difficult part](#difficult-part)
	- [How to test](#how-to-test)
	- [Possible improvements](#possible-improvements)
		- [AWS Lambda and API Gateway](#aws-lambda-and-api-gateway)
		- [AWS CloudWatch](#aws-cloudwatch)

<!-- /MarkdownTOC -->

# Related Presentations
* [Leveraging Splunk to Manage Your AWS Environment](http://technology.finra.org/articles/video/using-splunk-to-manage-aws.html)
* [FINRA takes control of its big data challenges with Splunk Cloud](http://diginomica.com/2014/10/07/finra-takes-control-big-data-challenges-splunk-cloud/)


# Background
## FINRA
### Def
* FINRA, one of the largest independent securities regulators in the United States, was established to monitor and regulate financial trading practices. FINRA protects investors by regulating brokers and brokerage firms by monitoring trading on U.S. stock markets. 
* The regulatory body is responsible for all of the data that relates to trades occurring in the United States. To put that into perspective, this equates to dealing with more data than either Twitter or Visa heft on a daily basis. We are talking petabytes of data.

### Company Missions
* FINRA watches over 6 billion shares traded on the stock market each day (Or up to 75 billion events per day, over 20 petabytes of storage). FINRA uses the monitored large amounts of data to
	- Reconstruct the market from trillions of events
		+ Data from broker-dealers and exchanges
		+ Equities, Options, Fixed Income
		+ Build a graph of market order events
	- Analyze the data looking for financial fraud
		+ Insider trading, layering, cross-product manipulation, front running & many more
		+ Looking for a needle in a haystack

### Company Visions
* To respond to rapidly changing market dynamics, FINRA moved about 75 percent of its operations to Amazon Web Services, using AWS to capture, analyze, and store a daily influx of 75 billion records. The company estimates it will save up to $20 million annually by using AWS instead of a physical data center infrastructure.
* FINRA has a goal to move its on-premises data center to the cloud within 5 years. AWS is currently FINRA's primary cloud provider. 

### Finra and AWS
* AWS services used by FINRA
	- Data management
		+ Amazon RDS
		+ Amazon S3
		+ Amazon Glacier
		+ Amazon EC2
	- Data analytics
		+ Machine learning
		+ Presto
		+ Amazon EMR
		+ Amazon Redshift
		+ Hive
* Usage statistics
	- 30k+ EC2 nodes per day. 93% of EC2 usage is EMR based
	- 20Pb+ storage (Amazon S3, Amazon Glacier)
	- 60% PROD, 25% QC/UAT, 15% DEV
	- Node lifecycles
		+ 50%: Under 2h
		+ 35%: 2h to 5h
		+ 15%: Over 5h

## Team
* Our team is responsible for monitoring all security-related events happened inside FINRA. My team uses Splunk, AWS EMR, AWS IAM and AWS CloudTrail. Sample projects in our team includes

* Major projects in our team
	- Real-time AWS cost management
	- Security compliance

* Environment
	- Splunk cloud
		+ [AWS Splunk Cloud](http://diginomica.com/2014/10/07/finra-takes-control-big-data-challenges-splunk-cloud/)
		+ We were one of the first people to use Splunk Cloud. Our company in general has a good presence in cloud and we are one of the bigger customers of AWS. We wanted to build a SIEM, but we needed huge amounts of data storage and data volume storage, and if we were going to do it on premise then we would need to buy and manage this box and that box – it ends up that you spend a greater amount of time on maintenance of these things than you do adding value. We thought it was a good idea to offload that to Splunk – we are happy with the cloud. Let them own the base layer and we will start adding value on top of that. With cloud I can put an exact dollar amount on data. I can say that it is going to cost us $10,000 a year to give you that information – is it worth it? It makes those type of decisions a little bit easier.

## Project


# Technical components
## IAM
### Permission types
* Identity-based vs Resource-based

#### Users and groups
#### Roles

### Policies
#### Structure
#### Evaluation

## S3
### Comparisons
* S3 vs Glacier vs EBS vs DynamoDB vs RDS vs ElastiCache

### How is benchmarks designed

## CloudTrail
### Def
* AWS CloudTrail captures AWS API calls and related events made by or on behalf of an AWS account and delivers log files to an Amazon S3 bucket that you specify. 

### Use cases
* Operations: 
	- Track changes to AWS resources: Track creation, modification, and deletion of AWS resources such as Amazon EC2, VPC security groups and Amazon EBS volumes. 
	- Troubleshoot operational issues: Quickly identify the most recent changes made to resources in your environment.
		+ Who started that ec2 in development
		+ Who stopped that ec2 in production
* Security: Use log files as an input into log management and analysis solutions to perform security analysis and to detect user behavior patterns.
	- Was that change to the security group authorized
	- Why was that user added to the group
	- Why is this ID generating so many AuthFailure/AccessDenied
* Application: 
	- My application worked yesterday, what changed
	- Have I been added to the monitoring group yet

### Workflow
* AWS CloudTrail captures AWS API calls and related events made by or on behalf of an AWS account and delivers log files to an Amazon S3 bucket that you specify.
* You can also choose to receive Amazon SNS notifications each time a log file is delivered to your bucket. 

### Methods
* You can create a trail with the CloudTrail console, the AWS CLI, or the CloudTrail API. A trail is a configuration that enables logging of the AWS API activity and related events in your account.

### Log example
#### Information in a recorded API call
* Who made the API call
* When was the API call made
* What was the API call
* What were the resources that were acted up on in the API call
* Where was the API call made from


## CloudTrail + Splunk

### Responsibilities:
* True security requires collecting all data.
* AWS CloudTrail delivers valuable visibility into user account activity. 
* CloudTrail captures everything, but Splunk app allows for filtering. 


## Difficult part

## How to test

## Possible improvements
### AWS Lambda and API Gateway
### AWS CloudWatch
