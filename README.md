AWS Cheat Sheet **Work in progress - All contributions are welcome** 

# Volumes

## Describing volumes

```
aws ec2 describe-volumes
```

```
aws ec2 describe-volumes --filters  Name=status,Values=creating | available | in-use | deleting | deleted | error
```

```
aws ec2 describe-volumes --filters  Name=attachment.status,Values=attaching | attached | detaching | detached
```

```
aws ec2 describe-volumes --filters Name:'tag:Name',Values: ['some_values'] --profile <your_profile_name>
```

## Describing volumes using a different aws user profile

```
aws ec2 describe-volumes --filters  Name=status,Values=in-use  --profile <your_profile_name>
```

## Listing available volumes ids

```
aws ec2 describe-volumes --filters  Name=status,Values=available  --profile <your_profile_name>|grep VolumeId|awk '{print $2}' | tr '\n|,|"' ' '
```


## Deleting a volume

```
aws ec2 delete-volume --region eu-west-1 --volume-id vol-0bbed7fb364e168d2
```


## Deleting unused volumes (think before you type)

```
for x in $(aws ec2 describe-volumes --filters  Name=status,Values=available  --profile <your_profile_name>|grep VolumeId|awk '{print $2}' | tr ',|"' ' '); do aws ec2 delete-volume --region eu-west-1 --volume-id $x --profile <your_profile_name>; done
```


## Creating a snapshot

```
aws ec2 create-snapshot --volume-id <vol-id> --profile <your_profile_name>
```

```
aws ec2 create-snapshot --volume-id <vol-id> --description "snapshot-$(date +'%Y-%m-%d_%H-%M-%S')" --profile <your_profile_name>
```

## Creating an image (AMI)

```
aws ec2 create-image --instance-id i-0ab8968eb4daa3859 --name "image-$(date +'%Y-%m-%d_%H-%M-%S')" --description "image-$(date +'%Y-%m-%d_%H-%M-%S')" --profile <your_profile_name>
```


## Creating AMI without reboot

```
aws ec2 create-image --instance-id i-0ab8968eb4daa3859 --name "image-$(date +'%Y-%m-%d_%H-%M-%S')" --description "image-$(date +'%Y-%m-%d_%H-%M-%S')" --no-reboot --profile <your_profile_name>
```

# Lambda

## Using AWS Lambda with Scheduled Events

```
sid=Sid$(date +%Y%m%d%H%M%S); aws lambda add-permission --statement-id $sid --action 'lambda:InvokeFunction' --principal events.amazonaws.com --source-arn arn:aws:events:eu-west-1:<953414735923>:rule/AWSLambdaBasicExecutionRole --function-name function:<awsents> --region eu-west-1
```


# IAM

## List Users
 
```
aws iam list-users
```


## List Policies

```
aws  iam list-policies
```

## List Groups

```
aws iam list-groups
```

## Get Users In A Group

```
aws iam get-group --group-name <group_name>
```


## Describing A Policy

```
aws iam get-policy --policy-arn arn:aws:iam::aws:policy/<policy_name>
```


## List Access Keys

```
aws iam list-access-keys
```


## List Keys

```
aws iam list-access-keys
```


## List The Access Key IDs For An IAM User

```
aws iam list-access-keys --user-name <user_name>
```


## List The SSH Public Keys For A User

```
aws iam list-ssh-public-keys --user-name <user_name>
```


# S3 API

## Listing Buckets

```
aws s3api list-buckets
```


## Listing Only Bucket Names

```
aws s3api list-buckets --query 'Buckets[].Name'
```

# VPC

## Creating A VPC

```
aws ec2 create-vpc --cidr-block <cidr_block> --regiosn <region>
```

e.g

```
aws ec2 create-vpc --cidr-block 10.0.0.0/16 --region eu-west-1
```

## Allowing DNS hostnames

```
aws ec2 modify-vpc-attribute --vpc-id <vpc_id> --enable-dns-hostnames "{\"Value\":true}" --region <region>
```

# Subnets 

## Creating A Subnet

```
aws ec2 create-subnet --vpc-id <vpc_id> --cidr-block <cidr_block> --availability-zone <availability_zone> --region <region>
```

## Auto Assigning Public IPs To Instances In A Public Subnet

```
aws ec2 modify-subnet-attribute --subnet-id <subnet_id> --map-public-ip-on-launch --region <region>
```

# Internet Gateway

## Creating An IGW

```
aws ec2 create-internet-gateway --region <region>
```

## Attaching An IGW to A VPC

```
aws ec2 attach-internet-gateway --internet-gateway-id <igw_id> --vpc-id <vpc_id> --region <region>
```

# NAT

## Setting Up A NAT Gateway

Allocate Elastic IP ``` aws ec2 allocate-address --domain vpc --region <region> ``` then use the AllocationId to create the NAT Gateway for the public zone in <region>: ``` aws ec2 create-nat-gateway --subnet-id <subnet_id> --allocation-id <allocation_id> --region <region> ```

# Route Tables

## Creating A Public Route Table

Create the Route Table: ``` aws ec2 create-route-table --vpc-id <vpc_id> --region <region> ``` then create a route for an Internet Gateway. Now, use the outputted Route Table ID: ``` aws ec2 create-route --route-table-id <route_table_id> --destination-cidr-block 0.0.0.0/0 --gateway-id <igw_id> --region <region> ```.

Finally, associate the public subnet with the Route Table: ``` aws ec2 associate-route-table --route-table-id <route_table_id> --subnet-id <subnet_id> --region <region> ```.

## Creating A Private Route Tables

Create the Route Table: ``` aws ec2 create-route-table --vpc-id <vpc_id> --region <region> ``` then create a route that points to a NAT Gateway ``` aws ec2 create-route --route-table-id <route_table_id> --destination-cidr-block 0.0.0.0/0 --nat-gateway-id <net_gateway_id> --region <region> ```.

Finally, associate the subnet ``` aws ec2 associate-route-table --route-table-id <route_table_id> --subnet-id <subnet_id> --region <region> ```.
