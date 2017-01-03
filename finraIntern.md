# Finra intern project - Role-based Access Control System on AWS Cloud
<!-- MarkdownTOC -->

- [Description](#description)
- [Background](#background)
	- [Company](#company)
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
		- [Workflow](#workflow)
		- [Log example](#log-example)
		- [CloudTrail + Splunk](#cloudtrail--splunk)
	- [Difficult part](#difficult-part)
	- [How to test](#how-to-test)
	- [Possible improvements](#possible-improvements)
		- [AWS Lambda and API Gateway](#aws-lambda-and-api-gateway)

<!-- /MarkdownTOC -->

# Description
1. Mentorship
2. redo your project, how do you improve it this time.
简单问了问。有几个问题自己以前没仔细考虑过。最大的难点？在test过程中哪部分最容易出错？如果重来，你会对哪部分做修改？大家有时间可以考虑一下啊。
3. Engineering manager warm up
然后问了个high level的问题说，一个user enter company or univeristy information when signing on, how to help the user better locate his company/univeristy? 我先说用hashtable存company/university 和id pairs，然后用trie实现auto-completion帮助user完成Look-up。他说对，但是太detail了，问High-level怎么快速locate？我说可以combine location, rank of the top selected company/university, connection’s company/univerity, logo来推荐。
他又问如果用户填资料的时候没有选对应的选项，自己填了一个怎么办？我说可以暂时存null，之后再让用户complete information
4. Talked about Interests and why LinkedIn. I only asked a couple of questions at the end. Only lasted 50 mins for this round.

# Background
## Company

## Team

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
### Workflow
### Log example
### CloudTrail + Splunk

## Difficult part

## How to test

## Possible improvements
### AWS Lambda and API Gateway
