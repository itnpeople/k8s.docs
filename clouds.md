# Clouds

## aws

```
▒ aws configure                                            # cli 연결정보 설정

▒ aws ec2 describe-security-groups                         # security group 조회
▒ aws ec2 describe-vpcs --vpc-ids=vpc-0fb1ad52551f100a1    # vpc 아이로 조회
```


### AMI

```
▒ aws ec2 describe-images
▒ aws ec2 describe-images --owners self amazon 

#  현재 Amazon Linux 2 AMI 검색
▒  aws ec2 describe-images --owners self amazon \
    --filters 'Name=name,Values=amzn2-ami-hvm-2.0.????????.?-x86_64-gp2' 'Name=state,Values=available' \
    --output text
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