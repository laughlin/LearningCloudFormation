@title[CloudFormation and You!]

# CloudFormation

---

# Overview

* What is Amazon Web Services?
* What is CloudFormation?
* Setup
* Run through some code...

---

# Amazon Web Services

* Virtual Hardware provided at low costs
* A vast array of services

---

# What is CloudFormation

* Scripting Stack creation

---

# Setup

* If you have not done so yet
  * create an [account](https://portal.aws.amazon.com/billing/signup#/start)
  * **NOTE** We will stick to free tier items

---

## Hello World!

```yml

AWSTemplateFormatVersion: '2010-09-09'

Description:
  'Basic EC2'

Resources:
  MyEC2:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: 'ami-965e6bf3'
      InstanceType: 't2.micro'
      AvailabilityZone: 'us-east-2a'

```

@[2](Define the CloudFormation Sytax Version.)
@[4,5](Stack Description)
@[7-13](An EC2 Instance, all free)

--- 