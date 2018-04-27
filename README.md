AWS Cheat Sheet **Work in progress - All contributions are welcome** 

# Volumes

### Describing volumes

```
aws ec2 describe-volumes
```

Describing filtered volumes:

```
aws ec2 describe-volumes --filters  Name=status,Values=creating | available | in-use | deleting | deleted | error
```


e.g, describing all deleted volumes:

```
aws ec2 describe-volumes --filters  Name=status,Values=deleted
```

Filters can be applied to the attachment status:

```
aws ec2 describe-volumes --filters  Name=attachment.status,Values=attaching | attached | detaching | detached
```

e.g: describing all volumes with the status "attaching":


```
aws ec2 describe-volumes --filters  Name=attachment.status,Values=attaching
```


This is the generic form. Use --profile ```<your_profile_name> ```, if you have multiple AWS profiles or accounts.


```
aws ec2 describe-volumes --filters Name:'tag:Name',Values: ['some_values'] --profile <your_profile_name>
```

### Describing volumes using a different aws user profile

```
aws ec2 describe-volumes --filters  Name=status,Values=in-use  --profile <your_profile_name>
```

### Listing Available Volumes IDs


```
aws ec2 describe-volumes --filters  Name=status,Values=available |grep VolumeId|awk '{print $2}' | tr '\n|,|"' ' '
```

With "profile":

```
aws ec2 describe-volumes --filters  Name=status,Values=available  --profile <your_profile_name>|grep VolumeId|awk '{print $2}' | tr '\n|,|"' ' '
```


### Deleting a Volume

```
aws ec2 delete-volume --region <region> --volume-id <volume_id>
```


### Deleting Unused Volumes.. Think Before You Type :-)


```
for x in $(aws ec2 describe-volumes --filters  Name=status,Values=available  --profile <your_profile_name>|grep VolumeId|awk '{print $2}' | tr ',|"' ' '); do aws ec2 delete-volume --region <region> --volume-id $x; done
```

With "profile":

```
for x in $(aws ec2 describe-volumes --filters  Name=status,Values=available  --profile <your_profile_name>|grep VolumeId|awk '{print $2}' | tr ',|"' ' '); do aws ec2 delete-volume --region <region> --volume-id $x --profile <your_profile_name>; done
```


### Creating a Snapshot

```
aws ec2 create-snapshot --volume-id <vol-id>
```

```
aws ec2 create-snapshot --volume-id <vol-id> --description "snapshot-$(date +'%Y-%m-%d_%H-%M-%S')"
```

### Creating an Image (AMI)

```
aws ec2 create-image --instance-id <instance_id> --name "image-$(date +'%Y-%m-%d_%H-%M-%S')" --description "image-$(date +'%Y-%m-%d_%H-%M-%S')"
```


### Creating AMI Without Rebooting the Machine

```
aws ec2 create-image --instance-id <instance_id> --name "image-$(date +'%Y-%m-%d_%H-%M-%S')" --description "image-$(date +'%Y-%m-%d_%H-%M-%S')" --no-reboot
```

You are free to change the AMI name ``` image-$(date +'%Y-%m-%d_%H-%M-%S') ``` to a name of your choice.


# AMIs

### Listing AMI(s)

```
aws ec2 describe-images
```

### Describing AMI(s)

```
aws ec2 describe-images --image-ids <image_id> --profile <profile> --region <region>
```

e.g: 

```
aws ec2 describe-images --image-ids ami-e24dfa9f --profile terraform --region eu-west-3
```

### Listing Amazon AMIs

```
aws ec2 describe-images --owners amazon 
```

### Using Filters

e.g: Describing Windows AMIs that are backed by Amazon EBS.

```
aws ec2 describe-images --filters "Name=platform,Values=windows" "Name=root-device-type,Values=ebs"
```

e.g: Describing Ubuntu AMIs 

```
aws ec2 describe-images --filters "Name=name,Values=ubuntu*"
```

# Lambda

### Using AWS Lambda with Scheduled Events

```
sid=Sid$(date +%Y%m%d%H%M%S); aws lambda add-permission --statement-id $sid --action 'lambda:InvokeFunction' --principal events.amazonaws.com --source-arn arn:aws:events:<region>:<arn>:rule/AWSLambdaBasicExecutionRole --function-name function:<awsents> --region <region>
```


# IAM

### List Users
 
```
aws iam list-users
```


### List Policies

```
aws iam list-policies
```

### List Groups

```
aws iam list-groups
```

### Get Users in a  Group

```
aws iam get-group --group-name <group_name>
```


### Describing a Policy

```
aws iam get-policy --policy-arn arn:aws:iam::aws:policy/<policy_name>
```


### List Access Keys

```
aws iam list-access-keys
```


### List Keys

```
aws iam list-access-keys
```


### List the Access Key IDs for an IAM User

```
aws iam list-access-keys --user-name <user_name>
```


### List the SSH Public Keys for a User

```
aws iam list-ssh-public-keys --user-name <user_name>
```


# S3 API

### Listing Buckets

```
aws s3api list-buckets
```

Or

```
aws s3 ls
```


e.g

```
aws s3 ls --profile eon01
```


### Listing Only Bucket Names

```
aws s3api list-buckets --query 'Buckets[].Name'
```

### Getting a Bucket Region

```
aws s3api get-bucket-location --bucket <bucket_name>
```

e.g

```
aws s3api get-bucket-location --bucket practicalaws.com
```

### Listing the Content of a Bucket

```
aws s3 ls s3://<bucket_name> --region <region>
```

e.g

```
aws s3 ls s3://practicalaws.com

aws s3 ls s3://practicalaws.com --region eu-west-1
 
aws s3 ls s3://practicalaws.com --region eu-west-1 --profile eon01
```

### Syncing a Local Folder with a Bucket

```
aws s3 sync <local_path> s3://<bucket_name> 
```


e.g

```
aws s3 sync . s3://practicalaws.com --region eu-west-1
```

### Removing a File from a Bucket

```
aws s3 rm s3://<bucket_name>/<object_name>
```

e.g

```
aws s3 rm s3://practicalaws.com/temp.txt
```

### Deleting a Bucket

```
aws s3 rb s3://<bucket_name> --force
```

If the bucket is not empty, use --force.

e.g

```
aws s3 rb s3://practicalaws.com --force  
```

### Emptying a Bucket

```
aws s3 rm s3://<bucket_name>/<key_name> --recursive
```

e.g

In order to remove tempfiles/file1.txt and tempfiles/file2.txt from practicalaws.com bucket, use:

```
aws s3 rm s3://practicalaws.com/tempfiles --recursive
```

Remove all objects using:

```
aws s3 rm s3://practicalaws.com/tempfiles
```

# VPC

### Creating A VPC

```
aws ec2 create-vpc --cidr-block <cidr_block> --regiosn <region>
```

e.g

```
aws ec2 create-vpc --cidr-block 10.0.0.0/16 --region eu-west-1
```

### Allowing DNS hostnames

```
aws ec2 modify-vpc-attribute --vpc-id <vpc_id> --enable-dns-hostnames "{\"Value\":true}" --region <region>
```

# Subnets 

### Creating A Subnet

```
aws ec2 create-subnet --vpc-id <vpc_id> --cidr-block <cidr_block> --availability-zone <availability_zone> --region <region>
```

### Auto Assigning Public IPs To Instances In A Public Subnet

```
aws ec2 modify-subnet-attribute --subnet-id <subnet_id> --map-public-ip-on-launch --region <region>
```

# Internet Gateway

### Creating An IGW

```
aws ec2 create-internet-gateway --region <region>
```

### Attaching An IGW to A VPC

```
aws ec2 attach-internet-gateway --internet-gateway-id <igw_id> --vpc-id <vpc_id> --region <region>
```

# NAT

### Setting Up A NAT Gateway

Allocate Elastic IP

``` 
aws ec2 allocate-address --domain vpc --region <region> 
``` 

then use the AllocationId to create the NAT Gateway for the public zone in <region>

``` 
aws ec2 create-nat-gateway --subnet-id <subnet_id> --allocation-id <allocation_id> --region <region> 
```

# Route Tables

### Creating A Public Route Table

Create the Route Table: 

``` 
aws ec2 create-route-table --vpc-id <vpc_id> --region <region> 
``` 

then create a route for an Internet Gateway. 

Now, use the outputted Route Table ID: 

``` 
aws ec2 create-route --route-table-id <route_table_id> --destination-cidr-block 0.0.0.0/0 --gateway-id <igw_id> --region <region> 
```

Finally, associate the public subnet with the Route Table

``` 
aws ec2 associate-route-table --route-table-id <route_table_id> --subnet-id <subnet_id> --region <region>
```

### Creating A Private Route Tables

Create the Route Table

``` 
aws ec2 create-route-table --vpc-id <vpc_id> --region <region> 
``` 

then create a route that points to a NAT Gateway 

``` 
aws ec2 create-route --route-table-id <route_table_id> --destination-cidr-block 0.0.0.0/0 --nat-gateway-id <net_gateway_id> --region <region> 
```

Finally, associate the subnet 

``` 
aws ec2 associate-route-table --route-table-id <route_table_id> --subnet-id <subnet_id> --region <region> 
```

# CloudFront

### Listing Distributions

In some cases, you need to setup this first:

```
aws configure set preview.cloudfront true
```

Then:

```
aws cloudfront list-distributions
```

### Invalidating Files From a Distribution

To invalidate index and error HTML files from the distribution with the ID Z2W2LX9VBMAPRX:

```
aws cloudfront create-invalidation --distribution-id Z2W2LX9VBMAPRX  --paths /index.html /error.html
```

To invalidate everything in the distribution:

```
aws cloudfront create-invalidation --distribution-id Z2W2LX9VBMAPRX  --paths '/*'
```

