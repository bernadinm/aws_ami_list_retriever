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

5. Output

```
rhel_7.4_eu-north-1 = rhel_7.4_ap-south-1 = "ami-e60e5a89"
rhel_7.4_eu-west-3 = "ami-dc13a4a1"
rhel_7.4_eu-west-2 = "ami-c1d2caa5"
rhel_7.4_eu-west-1 = "ami-c90195b0"
rhel_7.4_ap-northeast-2 = "ami-26f75748"
rhel_7.4_ap-northeast-1 = "ami-eb50cd8d"
rhel_7.4_sa-east-1 = "ami-0e88cb62"
rhel_7.4_ca-central-1 = "ami-c1cb4ea5"
rhel_7.4_ap-southeast-1 = "ami-5ae89f26"
rhel_7.4_ap-southeast-2 = "ami-1987757b"
rhel_7.4_eu-central-1 = "ami-194cdc76"
rhel_7.4_us-east-1 = "ami-26ebbc5c"
rhel_7.4_us-east-2 = "ami-0b1e356e"
rhel_7.4_us-west-1 = "ami-77a2a317"
rhel_7.4_us-west-2 = "ami-223f945a"
```
