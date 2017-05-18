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

## Listing available volumes ids

```
aws ec2 describe-volumes --filters  Name=status,Values=available  --profile <your_profile_name>|grep VolumeId|awk '{print $2}' | tr '\n|,|"' ' '
```


## Deleting a volume

```
aws ec2 delete-volume --region eu-west-1 --volume-id vol-0bbed7fb364e168d2
```


## Deleting unused volumes (think before you type)

```
for x in $(aws ec2 describe-volumes --filters  Name=status,Values=available  --profile <your_profile_name>|grep VolumeId|awk '{print $2}' | tr ',|"' ' '); do aws ec2 delete-volume --region eu-west-1 --volume-id $x --profile <your_profile_name>; done
```


## Creating a snapshot

```
aws ec2 create-snapshot --volume-id vol-051570739b83ce7da --profile <your_profile_name>
```

```
aws ec2 create-snapshot --volume-id vol-051570739b83ce7da --description "incruizer-websites-einstein-snapshot-$(date +'%Y-%m-%d_%H-%M-%S')" --profile <your_profile_name>
```

## Creating an image (AMI)

```
aws ec2 create-image --instance-id i-0ab8968eb4daa3859 --name "incruizer-websites-einstein-image-$(date +'%Y-%m-%d_%H-%M-%S')" --description "incruizer-websites-einstein-image-$(date +'%Y-%m-%d_%H-%M-%S')" --profile <your_profile_name>
```


## Creating AMI without reboot

```
aws ec2 create-image --instance-id i-0ab8968eb4daa3859 --name "incruizer-websites-einstein-image-$(date +'%Y-%m-%d_%H-%M-%S')" --description "incruizer-websites-einstein-image-$(date +'%Y-%m-%d_%H-%M-%S')" --no-reboot --profile <your_profile_name>
```

# Lambda

## Using AWS Lambda with Scheduled Events

```
sid=Sid$(date +%Y%m%d%H%M%S); aws lambda add-permission --statement-id $sid --action 'lambda:InvokeFunction' --principal events.amazonaws.com --source-arn arn:aws:events:eu-west-1:<953414735923>:rule/AWSLambdaBasicExecutionRole --function-name function:<awsents> --region eu-west-1
```


# IAM

## List Users

```
aws iam list-users
```


## List Policies

```
aws  iam list-policies
```


## List Groups

```
aws iam list-groups
```


## Get Users In A Group

```
aws iam get-group --group-name <group_name>
```


## Describing A Policy

```
aws iam get-policy --policy-arn arn:aws:iam::aws:policy/<policy_name>
```


## List Access Keys

```
aws iam list-access-keys
```


## List Keys

```
aws iam list-access-keys
```


## List The Access Key IDs For An IAM User

```
aws iam list-access-keys --user-name <user_name>
```


## List The SSH Public Keys For A User

```
aws iam list-ssh-public-keys --user-name <user_name>
```


# S3 API

## Listing Buckets

```
aws s3api list-buckets
```


## Listing Only Bucket Names

```
aws s3api list-buckets --query 'Buckets[].Name'
```






