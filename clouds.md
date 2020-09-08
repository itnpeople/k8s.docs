# Clouds

## aws

```
▒ aws configure                                            # cli 연결정보 설정

▒ aws ec2 describe-security-groups                         # security group 조회
▒ aws ec2 describe-vpcs --vpc-ids=vpc-0fb1ad52551f100a1    # vpc 아이로 조회
```


* AMI

```
▒ aws ec2 describe-images
▒ aws ec2 describe-images --owners self amazon 

#  현재 Amazon Linux 2 AMI 검색
▒  aws ec2 describe-images --owners '099720109477' \
    --filters 'Name=name,Values=ubuntu/images/hvm-ssd/ubuntu-bionic-18.04-amd64-server-*' 'Name=virtualization-type,Values=hvm' \
    --output text

# 099720109477:Canonical
▒  aws ec2 describe-images --owners '099720109477'\
    --filters 'Name=name,Values=*ubuntu-bionic-18.04-amd64-server-*' \
    --filters 'Name=virtualization-type,Values=hvm' \
    --query 'reverse(sort_by(Images, &CreationDate))[:1].ImageId'

```



## google

```
# 이미지 조회
▒ gcloud compute images list --filter="family:ubuntu"

# machine type 확인
▒ gcloud compute machine-types list --filter="zone:asia-northeast3"

# service acoount 확인
▒ gcloud iam service-accounts list
```