# aws_ami_retriever 
Tool to help generate all AMI's by owner on every region

## Prerequisites
 - AWS Cli 
 - AWS Access Keys
 - AWS EC2 Permissions

## Commands

1. Create a list of all the providers you want your tool to list AMIs for.

```bash
$ cat trusted_image_owners.log
CoreOS = "595879546273"
RHEL = "309956199498"
Centos = "679593333241"
```

2. Configure your AWS CLI

```
$ aws configure
AWS Access Key ID [****************7GFC]:
AWS Secret Access Key [****************IBAi]:
Default region name [None]: us-west-2
```

3. Retrieve all AMIs for each region by the trusted creators of the AMI below:

```bash
OWNERLIST=$(for owner in $(cat trusted_image_owners.log | awk '{print $3}'); do echo "--owner $owner";done | xargs); for region in $(aws ec2 describe-regions | grep RegionName | awk '{print $2}'); do aws ec2 describe-images --filters Name=virtualization-type,Values=hvm --region ${region//\"} $OWNERLIST > describe-images.${region//\"}.log; done
```

4. Return the OS by description shared across a specific version of OS you want to retrieve. In my case, I want to retrieve a OS named `RHEL-7.4_HVM-20180103-x86_64-2-Hourly2-GP2` from this list of images. Inspect the images that you want to retrieve yourself and query it.

```bash
for region in $(aws ec2 describe-regions | grep RegionName | awk '{print $2}'); do echo -n "rhel_7.4_${region//\"} = "; jq '.Images[] | select(.Name =="RHEL-7.4_HVM-20180103-x86_64-2-Hourly2-GP2") | .ImageId' describe-images.${region//\"}.log; done
```
