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

## Lesson Two
#### Secure Access

First we need to create a Key Pair for authentication:
1. Navigate to https://console.aws.amazon.com/ec2/
1. Network & Security
1. Key Pairs
1. Create Key Pair
1. Safely store the .pem file

---

## Lesson Two
#### Allow SSH Connections

1. EC2 Dashboard
1. Network & Security
1. Select Security Group
1. Inboud traffic
1. Edit - add ssh rule

---

## Lesson Two
#### Connecting over SSH - Mac

<p><span class="menu-title slide-title">~/.ssh/config</span></p>
```
Host lunchLearn
 HostName ec2-18-221-230-191.us-east-2.compute.amazonaws.com
 User ubuntu
 IdentityFile /workspace/learningCloudFormation/LunchLearn.pem
 IdentitiesOnly yes
```

@[1](Custom Name)
@[2](HostName of your instance)
@[3](Default User for our instance type)
@[4](Filepath of your saved .pem file)
@[5](Force SSH to use the .pem file)

---

## Lesson Two
#### Connecting over SSH - Windows

Amazon has wonderful Putty instructions [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html), but can be summed up as:

1. Convert the .pem file to a .ppk
  1. Launch Puttygen
  1. Generate an RSA
  1. Load the .pem file
  1. Safely save the private key

---

## Lesson Two
#### Connection over SSH - Windows Part 2

1. Create a new Putty connection using `ubutu@hostname`
1. Change the connection type to SSH and port 22
1. Under Connection -> SSH -> Auth, add your newly created ppk

---

## Lesson Two
#### Associating your EC2

```yml
Resources:
  MyEC2:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: 'ami-965e6bf3'
      InstanceType: 't2.micro'
      AvailabilityZone: 'us-east-2a'
      KeyName: # Your Key Name
```

@[1-7](The familiar EC2 syntax)
@[8](The Authetication Key Association)

