VPC Configuration

Example:

## Create a VPC with CIDR-BLOCK: 

vpc-lambda
172.30.0.0/16


## Create Subnets:

VPC|CIDR|Tag name|Availability zone
---|---|---|---
vpc-lambda  |	(172.30.0.0/24) | lambda-subnet-to-nat-1  |  us-east-1a
vpc-lambda  |	(172.30.1.0/24) | lambda-subnet-to-nat-2  |  us-east-1c
vpc-lambda  |	(172.30.2.0/24) | lambda-subnet-to-nat-3  |  us-east-1d
vpc-lambda  |	(172.30.3.0/24) | lambda-subnet-to-nat-4  |  us-east-1e
vpc-lambda  |   (172.30.4.0/24) | lambda-subnet-to-igw-1  |  us-east-1a
	
## Create Route tables:

lambda-rt-to-nat pointing to nat gateway

lambda-rt-to-igw pointing to internet gateway


## In each subnets we have to assign  their corresponding route table.

we select first the subnet and into "Route table" press edit and into "Change to" we select the proper route table

Subnet name|Route table name
---|---
lambda-subnet-to-nat-1  |  lambda-rt-to-nat
lambda-subnet-to-nat-2	|  lambda-rt-to-nat
lambda-subnet-to-nat-3	|  lambda-rt-to-nat
lambda-subnet-to-nat-4	|  lambda-rt-to-nat
lambda-subnet-to-igw	|  lambda-rt-to-igw

## Create a NAT gateway.

VPC Dashboard > NAT Gateways > Create NAT Gateway

Your going to want click Create NAT Gateway and set the Subnet* to lambda-subnet-point-to-igw, and Create New EIP.

## Set up lambda function

> Lambda->Functions->function->Configuration->Advanced Settings


VPC

select: vpc-lambda

Subnets:

Select nat (private) subnets

subnet names
lambda-subnet-to-nat-1
lambda-subnet-to-nat-2
lambda-subnet-to-nat-3
lambda-subnet-to-nat-4

Security groups
select:
sg-default

Inbound rules | Outbound rules


Here we need to check into outbound rules it has ports ALL and Destination 0.0.0.0/0
