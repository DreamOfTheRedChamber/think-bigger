# Permissions Compliance System on AWS
<!-- MarkdownTOC -->

- [Related Presentations](#related-presentations)
- [Background](#background)
	- [FINRA](#finra)
		- [Def](#def)
		- [Company Missions](#company-missions)
		- [Company Visions](#company-visions)
		- [Finra and AWS](#finra-and-aws)
	- [Team](#team)
		- [AWS shared responsibility](#aws-shared-responsibility)
			- [AWS responsibility - Security of the cloud](#aws-responsibility---security-of-the-cloud)
			- [Your responsibility - Security in the cloud](#your-responsibility---security-in-the-cloud)
		- [Splunk cloud](#splunk-cloud)
	- [Project](#project)
- [Technical components](#technical-components)
	- [IAM](#iam)
		- [Def](#def-1)
		- [Access control policy](#access-control-policy)
			- [Def](#def-2)
			- [Structure](#structure)
				- [Principals](#principals)
				- [Actions](#actions)
				- [Resources](#resources)
				- [Condition](#condition)
			- [Types](#types)
				- [Resource-based policies vs Identity-based policies](#resource-based-policies-vs-identity-based-policies)
				- [Managed policies vs Inline policies](#managed-policies-vs-inline-policies)
			- [Policy example](#policy-example)
			- [Evaluating policies](#evaluating-policies)
		- [Identities](#identities)
			- [Root user](#root-user)
			- [Users](#users)
			- [Groups](#groups)
			- [Roles](#roles)
				- [Use case](#use-case)
			- [Comparison](#comparison)
	- [S3](#s3)
		- [Comparisons](#comparisons)
		- [How is benchmarks designed](#how-is-benchmarks-designed)
	- [CloudTrail](#cloudtrail)
		- [Def](#def-3)
		- [Use cases](#use-cases)
		- [Workflow](#workflow)
		- [Methods](#methods)
		- [Log example](#log-example)
			- [Information in a recorded API call](#information-in-a-recorded-api-call)
		- [Monitor and receive notifications](#monitor-and-receive-notifications)
	- [CloudTrail + Splunk](#cloudtrail--splunk)
		- [Responsibilities:](#responsibilities)
	- [Difficult part](#difficult-part)
	- [How to test](#how-to-test)
	- [Possible improvements](#possible-improvements)
		- [AWS Lambda and API Gateway](#aws-lambda-and-api-gateway)
			- [Lambda](#lambda)
			- [API Gateway](#api-gateway)
			- [Combined](#combined)
		- [AWS CloudWatch logs](#aws-cloudwatch-logs)

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

### AWS shared responsibility
#### AWS responsibility - Security of the cloud
* Protecting the network through automated monitoring systems and robust internet access to prevent distributed denial of service (DDos) attacks. 
* Performing background checks on employees who have access to sensitive areas.
* Decommissioning storage services by physically destroying them after end of life.
* Ensuring physical and environmental security of data centers, including fire protection and security staff.

#### Your responsibility - Security in the cloud
* Encrypting network traffic to prevent attackers from reading or manipulating data (for example, HTTPS)
* Configuring a firewall for your virtual private network that controls incoming and outgoing traffic with security groups and ACLs.
* Managing patches for the OS and additional software on virtual servers.
* Implementing access management that restricts access to AWS resources like S3 and EC2 to maintain a minimum with IAM.  

### Splunk cloud
* [AWS Splunk Cloud](http://diginomica.com/2014/10/07/finra-takes-control-big-data-challenges-splunk-cloud/)
* We were one of the first people to use Splunk Cloud. Our company in general has a good presence in cloud and we are one of the bigger customers of AWS. We wanted to build a SIEM, but we needed huge amounts of data storage and data volume storage, and if we were going to do it on premise then we would need to buy and manage this box and that box – it ends up that you spend a greater amount of time on maintenance of these things than you do adding value. We thought it was a good idea to offload that to Splunk – we are happy with the cloud. Let them own the base layer and we will start adding value on top of that. With cloud I can put an exact dollar amount on data. I can say that it is going to cost us $10,000 a year to give you that information – is it worth it? It makes those type of decisions a little bit easier.

## Project
* The purpose of this project is as the first step in building a security 

# Technical components
## IAM
### Def
* Identity Access and Management service provides granular control for your AWS account. 
	- Users and Groups
	- Unique security credentials
	- Temporary security credentials
	- Policies & permissions
	- Roles
	- Multi-factor authentication

### Access control policy
#### Def
* You can grant or deny access by defining: 
	- Who can access your resources
	- What actions they can take
	- Which resources they can access
	- How will they access your resources
* The policy language is about authorization. 

#### Structure
* Policies are JSON-formatted documents, which contain statements which specify 
	- What actions a principal can perform
	- Which resources can be accessed

```json
{
	"Statement" : [
		{
			"Effect": "Allow",
			"Action": ["s3: Get", "s3: List"],
			"Resource": "*"
		}
	]
}
```

##### Principals
* Principal is an entity that is allowed or denied access to a resource. 
* Principal element is required for resource-based policies.

##### Actions
* Actions describes the type of access that should be allowed or denied. 
* Statements must include either an Action or NotAction element.

##### Resources
* Resources describe objects that are being requested. 
* Statements must include either a Resource or a NotResource element. 

##### Condition
* Allows a user to access a resource under the following conditions:
	- The time is after 12:00 p.m. on 8/16/2013
	- The time is before 3:00 p.m. on 8/16/2013
	- The request comes from an IP address in the 192.0.2.0 /24 or 203.0.113.0 /24 range

#### Types
##### Resource-based policies vs Identity-based policies
* For some AWS services, you can grant cross-account access to your resources. To do this, you attach a policy directly to the resource that you want to share, instead of using a role as a proxy. The resource that you want to share must support resource-based policies. Unlike a user-based policy, a resource-based policy specifies who (in the form of a list of AWS account ID numbers) can access that resource.

* Advantage: 
	- Cross-account access with a resource-based policy has an advantage over a role. With a resource that is accessed through a resource-based policy, the user still works in the trusted account and does not have to give up his or her user permissions in place of the role permissions. In other words, the user continues to have access to resources in the trusted account at the same time as he or she has access to the resource in the trusting account. This is useful for tasks such as copying information to or from the shared resource in the other account.
* Disadvantage: 
	- The disadvantage is that not all services support resource-based policies. A few of the AWS services that support resource-based policies are listed here:
		+ Amazon S3 buckets – The policy is attached to the bucket, but the policy controls access to both the bucket and the objects in it. For more information, go to Access Control in the Amazon Simple Storage Service Developer Guide.
		+ Amazon Simple Notification Service (Amazon SNS) topics – For more information, go to Managing Access to Your Amazon SNS Topics in the Amazon Simple Notification Service Developer Guide.
		+ Amazon Simple Queue Service (Amazon SQS) queues – For more information, go to Appendix: The Access Policy Language in the Amazon Simple Queue Service Developer Guide.
		+ For a complete list of the growing number of AWS services that support attaching permission policies to resources instead of principals, see AWS Services That Work with IAM and look for the services that have Yes in the Resource Based column.

##### Managed policies vs Inline policies
* Using IAM, you apply permissions to IAM users, groups, and roles (which we refer to as principal entities) by creating policies. You can create two types of IAM, oridentity-based policies:
	- Managed policies – Standalone policies that you can attach to multiple users, groups, and roles in your AWS account. Managed policies apply only to identities (users, groups, and roles) - not resources. You can use two types of managed policies:
		+ AWS managed policies – Managed policies that are created and managed by AWS. If you are new to using policies, we recommend that you start by using AWS managed policies.
		+ Customer managed policies – Managed policies that you create and manage in your AWS account. Using customer managed policies, you have more precise control over your policies than when using AWS managed policies.
	- Inline policies – Policies that you create and manage, and that are embedded directly into a single user, group, or role. Resource-based policies are another form of inline policy. Resource-based policies are not discussed here. For more information about resource-based policies, see Identity-Based (IAM) Permissions and Resource-Based Permissions.
* Identity-based (IAM) policies can be either inline or managed. Resource-based policies are attached to the resources (inline) only and are not managed.

Generally speaking, the content of the policies is the same in all cases—each kind of policy defines a set of permissions using a common structure and a common syntax.

#### Policy example

#### Evaluating policies
* [Slides 46](http://www.slideshare.net/AmazonWebServices/mastering-access-control-policies-sec302-aws-reinvent-2013/7)

### Identities
#### Root user
* Root account has unrestricted access to all resources within the account, including access to billing information. 

#### Users
* An IAM user is an entity that you create in AWS to represent the person or service that uses it to interact with AWS. A user in AWS consists of a name and credentials.

* You can access AWS in different ways using the different types of credentials that can be associated with a user:
	- Console password: A password that the user can type to sign in to interactive sessions such as the AWS Management Console.
	- Access keys: An access key is the combination of an access key ID and a secret access key. You can assign two to a user at a time. These can be used to make programmatic calls to AWS when using the API in program code or at a command prompt when using the AWS CLI or the AWS PowerShell tools.

#### Groups
* An IAM group is a collection of IAM users. Groups let you specify permissions for multiple users, which can make it easier to manage the permissions for those users. 
	- For example, you could have a group called Admins and give that group the types of permissions that administrators typically need. Any user in that group automatically has the permissions that are assigned to the group. If a new user joins your organization and needs administrator privileges, you can assign the appropriate permissions by adding the user to that group. Similarly, if a person changes jobs in your organization, instead of editing that user's permissions, you can remove him or her from the old groups and add him or her to the appropriate new groups.
	- Note that a group is not truly an "identity" in IAM because it cannot be identified as a Principal in a permission policy. It is simply a way to attach policies to multiple users at one time.

* Following are some important characteristics of groups:
	- A group can contain many users, and a user can belong to multiple groups.
	- Groups can't be nested; they can contain only users, not other groups.
	- There's no default group that automatically includes all users in the AWS account. If you want to have a group like that, you need to create it and assign each new user to it.
	- There's a limit to the number of groups you can have, and a limit to how many groups a user can be in. For more information, see Limitations on IAM Entities and Objects.

* [A group example](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html)

#### Roles
* An IAM role is similar to a user, in that it is an AWS identity with permission policies that determine what the identity can and cannot do in AWS. However, instead of being uniquely associated with one person, a role is intended to be assumable by anyone who needs it. Also, a role does not have any credentials (password or access keys) associated with it. Instead, if a user is assigned to a role, access keys are created dynamically and provided to the user.

##### Use case
* You can use roles to delegate access to users, applications, or services that don't normally have access to your AWS resources. For example, you might want to grant users in your AWS account access to resources they don't usually have, or grant users in one AWS account access to resources in another account. 
* You might want to allow a mobile app to use AWS resources, but not want to embed AWS keys within the app (where they can be difficult to rotate and where users can potentially extract them). Sometimes you want to give AWS access to users who already have identities defined outside of AWS, such as in your corporate directory. Or, you might want to grant access to your account to third parties so that they can perform an audit on your resources.
* For these scenarios, you can delegate access to AWS resources using an IAM role. This section introduces roles and the different ways you can use them, when and how to choose among approaches, and how to create, manage, switch to (or assume), and delete roles.

#### Comparison

| Categories                             | Root user             | IAM user | IAM role | 
|----------------------------------------|-----------------------|----------|----------| 
| Can have a password                    | Always                | Yes      | No       | 
| Can have an access key                 | Yes (not recommended) | Yes      | No       | 
| Can belong to a group                  | No                    | Yes      | No       | 
| Can be associated with an EC2 instance | No                    | No       | Yes      | 


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

### Monitor and receive notifications
* You can monitor any specific event recorded by CloudTrail and receive notification from CloudWatch. Should monitor for security or network related events that are likely to have a high blast radius. 
* Popular examples based on customer feedback
	- Creation, deletion and modification of security groups and VPCs.
	- Changes to IAM policies or S3 bucket policies.
	- Failed AWS management console sign-in events.
	- API calls that resulted in authorization failures. 
	- Launching, terminating, stopping, starting and rebooting EC2 instances.
* Use fully defined and pre-built CloudFormation template to get started. 

## CloudTrail + Splunk

### Responsibilities:
* True security requires collecting all data.
* AWS CloudTrail delivers valuable visibility into user account activity. 
* CloudTrail captures everything, but Splunk app allows for filtering. 

## Difficult part
* How to finish it within three months

## How to test
* AWS policy generator
* AWS policy simulator

## Possible improvements
### AWS Lambda and API Gateway
#### Lambda
* AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume - there is no charge when your code is not running. With Lambda, you can run code for virtually any type of application or backend service - all with zero administration. Just upload your code and Lambda takes care of everything required to run and scale your code with high availability. You can set up your code to automatically trigger from other AWS services or call it directly from any web or mobile app.

#### API Gateway 
* Amazon API Gateway supports the following two major functionalities:
	- It lets you create, manage and host a RESTful API to expose AWS Lambda functions, HTTP endpoints as well as other services from the AWS family including, but not limited to, Amazon DynamoDB, Amazon S3 Amazon Kinesis. You can use this feature through the API Gateway REST API requests and responses, the API Gateway console, AWS Command-Line Interface (CLI), or an API Gateway SDK of supported platforms/languages. This feature is sometimes referred to as the API Gateway control service.
	- It lets you or 3rd-party app developer to call a deployed API to access the integrated back-end features, using standard HTTP protocols or a platform- or language-specific SDK generated by API Gateway for the API. This feature is sometimes known as the API Gateway execution service.

#### Combined
* AWS Lambda provides an easy way to build back ends without managing servers. API Gateway and Lambda together can be powerful to create and deploy serverless Web applications. In this walkthrough, you learn how to create Lambda functions and build an API Gateway API to enable a Web client to call the Lambda functions synchronously. For more information about Lambda, see the AWS Lambda Developer Guide. For information about asynchronous invocation of Lambda functions, see Create an API as a Lambda Proxy.

### AWS CloudWatch logs
* One of the ways that you can work with CloudTrail logs is to monitor them by sending them to CloudWatch Logs. For a trail that is enabled in all regions in your account, CloudTrail sends log files from all those regions to a CloudWatch Logs log group. You define CloudWatch Logs metric filters that will evaluate your CloudTrail log events for matches in terms, phrases, or values. You assign CloudWatch metrics to the metric filters. You also create CloudWatch alarms that are triggered according to thresholds and time periods that you specify. You can configure an alarm to send a notification when the alarm is triggered so that you can take immediate action. You can also configure CloudWatch to automatically perform an action in response to an alarm. CloudTrail events are protected by SSL encryption as they are delivered from CloudTrail to the CloudWatch Logs log grou

* Advantages of CloudWatch logs: What makes CloudWatch Logs more preferable over other third party tools?
	- CloudWatch is the single platform to monitor resource usage and logs.
	- CloudWatch Logs pricing is based on pay as you use model which may turn out to be cheaper than third party tools that work on per node licence model. Here you will be paying for log storage and bandwidth used to upload the files.

