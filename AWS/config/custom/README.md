<h1> Overview </h1>
AWS Config is a service that enables you to assess, audit, and evaluate the configurations of your AWS resources. Config continuously monitors and records your AWS resource configurations and allows you to automate the evaluation of recorded configurations against desired configurations

There are two types of AWS Config Rules

1.	Managed – These are rules predefined by AWS and can be used without making any changes
2.	Custom – These are rules created for custom use cases and to enable IT Organisation/Enterprise defined rules for managing resources in AWS

<h1> High Level Workflow </h1>

Below picture explains the high level workflow of creating and managing AWS Config (custom) Rules in CoreStack,

 
<h1> Steps to create a custom AWS Config Rule </h1>

Follow the link below to create AWS Config custom rules,

https://github.com/awslabs/aws-config-rules

Outcome of the Config Rule creation will be “parameters.json” and a python file. Name of the python file will be “RuleName” in ”parameters.json” file

	{
	  "Version": "1.0",
	  "Parameters": {
	    "RuleName": "AMI_NOT_PUBLIC_CHECK",
	    "SourceRuntime": "python3.6",
	    "CodeKey": "AMI_NOT_PUBLIC_CHECK.zip",
	    "InputParameters": "{}",
	    "OptionalParameters": "{}",
	    "SourcePeriodic": "Six_Hours"
	  },
	  "Tags": "[]"
	}

In this example, “AMI_NOT_PUBLIC_CHECK.py” will be the name of python file

Note: Though custom AWS Config Rules can be created in Python, Nodejs & Java, CoreStack supports only python. Support for Nodejs & Java will be available in the future CoreStack releases

<h1> How to onboard custom Config Rules into CoreStack </h1>

Onboarding custom Config Rules happens in two steps,

<h2> Step 1: Upload the Config Rule </h2>

Under the AWS Config custom folder in GitHub create a new folder for the new config rule. Add the parameters.json and the python file, that are received as part of the custom config rule creation, into the newly created folder (ex: AMI_NOT_PUBLIC_CHECK)

Now the folder AMI_NOT_PUBLIC_CHECK will contain two files “parameters.json” & “AMI_NOT_PUBLIC_CHECK.py”

<h2> Step 2: Add metadata needed for CoreStack in the file csmetadata.json </h2>
Create a new file csmetadata.json (filename to be same as mentioned) with the following structure,

	{
	   "description":"DESCRIPTION OF THE CONFIG RULE",
	   "severity":"high / medium / low",
	   "classification":"CoreStack Classification",
	   "sub_classification":"CoreStack Sub-Classification",
	   "resource_type":[
	      "resource_type_name1",
	      "resource_type_name2"
	   ]
	}

<h2> Details about each attribute </h2>

	1. description - Description to be used in CoreStack. As part of the AWS Custom Config Rules, there is no description maintained. In CoreStack, this description is required to provide detailed information to help others understand the purpose of the Config Rule
	2. severity - Severity of the config rule – must be one of the following (case-sensitive)
		• high
		• medium
		• low
	3. classification - Classification of the Config Rule in CoreStack terms. Must one be one of the allowed values (case-sensitive) mentioned in the table below
	4. sub_classifciation - Sub classification of the Config Rule in CoreStack terms. Must one be one of the allowed values (case-sensitive) mentioned in the table below
	5. resource_type - Name of the impacting resource type. It is typically the resource type name in AWS.

Service-provider::service-name::data-type-name is the format of resource types in AWS. CoreStack needs only the data-type-name to be mentioned as resource_type in the csmetadata.json

	For example, 
	AWS::EC2::Instance --> Instance
	AWS::EC2::EIP --> EIP
	AWS::ECS::Cluster --> Cluster

Refer to the link below for the AWS resource types,
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html



CoreStack Classification & Sub Classification reference

	Security - When the classification is mentioned as Security, then sub_classification must be one of the following
		• Access
		• Data
		• Network
		• Host
		• Application
		• End Point protection
		• Logging
		• Monitoring
	Cost - When the classification is mentioned as “Cost”, then sub_classification must be one of the following
		• Utilization
	Operation - When the classification is mentioned as “Operation”, then sub_classification must be one of the following
		• Performance
		• Availability
		• Standards

Sample csmetadata.json

Example 1:

	{
	"description": "Sample1: Test AWS ACM Certificate Expiration Check",
	"severity": "high",
	"classification": "Security",
	"sub_classification": "Monitoring",
	"resource_type": ["Certificate"]	
	}

Example 2: 

	{
	"description": "Sample2: Check AWS CMK Backing Key Rotation Enabled or not",
	"severity": "high",
	"classification": "Security",
	"sub_classification": "Data",
	"resource_type": ["Key"]	
	}
	
Example 3:

	{
	"description": "Sample3: Check AWS EC2 Association Compliance Status Check",
	"severity": "low",
	"classification": "Operation",
	"sub_classification": "Performance",
	"resource_type": ["Instance"]	
	}

When a AWS Config Rule is committed to GitHub with the above files, then CoreStack policy pipeline will automatically fetch them and create policies in CoreStack.

Any edits & deletes made to the policy will be processed by CoreStack policy pipeline and reflected in CoreStack. 

The newly created policy or updates to the policy can be viewed in the CoreStack portal or via CoreStack APIs
