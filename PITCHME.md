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

---

## Lesson Three
#### Now what?

Let's set up a simple web server!

---

## Lesson Three
#### Apache

```bash
sudo apt-get update
sudo apt-get install apache2
sudo service apache2 restart
```

---

## Lesson Three
#### Enable HTTP Connections

1. EC2 Dashboard
1. Network & Security
1. Select Security Group
1. Inboud traffic
1. Edit - add HTTP rule
1. Visit your new site!

--- 

## Lesson Four
#### Oh No! Your website got the reddit hug of death!

Q: How can we compensate for too much traffic?

---

## Lesson Four
#### Oh No! Your website got the reddit hug of death!

A: By adding more hosts
<br>
Easy Peasy
```
Resources:
  Frodo:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: 'ami-965e6bf3'
      InstanceType: 't2.micro'
      AvailabilityZone: 'us-east-2a'
      KeyName: 'Lunch&Learn'

  Sam:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: 'ami-965e6bf3'
      InstanceType: 't2.micro'
      AvailabilityZone: 'us-east-2a'
      KeyName: 'Lunch&Learn'
```

---

## Lesson Four
#### Share the Load, Mr Frodo.

We need to split the load between our hosts.
<br>
<br>
We can do that by adding an Elastic Load Balancer.
<br>
<br>
The ELB has three parts:
* The Load Balancer
* The Target Group
* The Listener

---

## Lesson Four
#### Elastic Load Balancer: Creating a Load Balancer
```
  LoadBalancer:
    Type: 'AWS::ElasticLoadBalancingV2::LoadBalancer'
    Properties:
      Name: 'll-balancer'
      SecurityGroups: 
        - # your Security Group
      Type: application
      Subnets: # your subnet id
```

@[5](Can be found in the AWS EC2 console under Security Groups)
@[7](Can be found in the AWS EC2 console under Network Interfaces)

---

## Lesson Four
#### Elastic Load Balancer: The Load Target

```
  LoadTarget:
    Type: 'AWS::ElasticLoadBalancingV2::TargetGroup'
    Properties:
      Name: 'LL-load-target'
      Port: 80
      Protocol: HTTP
      VpcId: # your VPC Id
      Targets:
        - 
          Id: 
            Ref: Frodo
          Port: 80
        - 
          Id:
            Ref: Sam
          Port: 80
```

@[5](The recieving port)
@[8-12](Your target hosts)

---

## Lesson Four
#### Elastic Load Balancer: Listening for Traffic

```
  ReviewHttpListener:
    Type: 'AWS::ElasticLoadBalancingV2::Listener'
    Properties:
      DefaultActions:
        - Type: forward
          TargetGroupArn:
            Ref: LoadTarget
      LoadBalancerArn:
        Ref: LoadBalancer
      Port: 80
      Protocol: HTTP
```

---

## Lesson Four
#### Manually deploying to the new host

Just like before, we can add our new host to our ssh configurations.

SSH to it, and install apache.

```bash
sudo apt-get update
sudo apt-get install apache2
sudo service apache2 restart
```

---

## Lesson Four
#### It Works!

Navigating to the Load Balancers public dns name will now redirect you to one of your two hosts!