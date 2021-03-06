#VPC Configuration

If we have to use Lambda service and we have to do a function where this function talks to an AWS services and It also needs to talk to the Internet, we need to create a new VPC using Nat Gateway configuration.

## Create a VPC with CIDR-BLOCK 


VPC name | CIDR
---|---
vpc-lambda | 172.30.0.0/16


## Create Subnets

> VPC Dashboard->Subnets


VPC|CIDR|Name tag|Availability zone
---|---|---|---
vpc-lambda  |	(172.30.0.0/24) | lambda-subnet-to-nat-1  |  us-east-1a
vpc-lambda  |	(172.30.1.0/24) | lambda-subnet-to-nat-2  |  us-east-1c
vpc-lambda  |	(172.30.2.0/24) | lambda-subnet-to-nat-3  |  us-east-1d
vpc-lambda  |	(172.30.3.0/24) | lambda-subnet-to-nat-4  |  us-east-1e
vpc-lambda  |   (172.30.4.0/24) | lambda-subnet-to-igw    |  us-east-1a
	
## Create Route tables

> VPC Dashboard->Route Tables

Name tag|VPC
---|---
lambda-rt-to-nat | vpc-lambda
lambda-rt-to-igw | vpc-lambda



## Adding route table on subnets

In each subnets we have to assign  their corresponding route table.

We select first the subnet and into `Route Table` tab,  press `Edit` button, and into `Change to` we select the proper route table we created before, then press `Save` button.

Subnet name|Route Table name
---|---
lambda-subnet-to-nat-1  |  lambda-rt-to-nat
lambda-subnet-to-nat-2	|  lambda-rt-to-nat
lambda-subnet-to-nat-3	|  lambda-rt-to-nat
lambda-subnet-to-nat-4	|  lambda-rt-to-nat
lambda-subnet-to-igw	|  lambda-rt-to-igw

## Create a IGW and attach to VPC

> VPC Dashboard->Internet Gateways->Create Internet Gateway

It's necessary to create a new internet gateway and then attach it to your VPC

IG|vpc
---|---
igw-lambda | vpc-lambda 

## Create a NAT gateway

> VPC Dashboard->NAT Gateways->Create NAT Gateway

We need to `Create NAT Gateway` and set the Subnet* to `lambda-subnet-to-igw`, and `Create New EIP`.

NG|Subnet
---|---
nat-xxxx | lambda-subnet-to-igw

Just we need to wait until status it is  `Available`

## Pointing Router Table to our NAT and IGW services created before


> VPC Dashboard->Route Tables


Now, we need to do this:

* lambda-rt-to-nat pointing to nat gateway
* lambda-rt-to-igw pointing to internet gateway

So, into every `Route Table` we need to go into `Routes` tab and Press `Edit` button, then we press `Add another rule` rule button and put this

Into lambda-rt-to-nat:

Destination|Target
---|---
0.0.0.0/0 | nat-xxxxxx 

And press `Save`

The same for lambda-rt-to-igw but we have to add `The Internet Gateway` here:

Destination|Target
---|---
0.0.0.0/0 | igw-xxxxxx

Name tag|VPC
---|---
lambda-rt-to-nat | nat-xxxxxx | 
lambda-rt-to-igw | igx-xxxxxx |

## Next step

Now, we are able to test Lambda functions with AWS SNS service, for example.


## Set up lambda function

> Lambda->Functions->function->Configuration->Advanced Settings


###VPC

`select: vpc-lambda`

###Subnets:

`Select nat (private) subnets`

subnet names |
--- |
lambda-subnet-to-nat-1 |
lambda-subnet-to-nat-2 |
lambda-subnet-to-nat-3 |
lambda-subnet-to-nat-4 |

###Security groups
`select:`
`sg-default`

Inbound rules | Outbound rules


Here we need to check into outbound rules:

`ports ALL and Destination 0.0.0.0/0`
