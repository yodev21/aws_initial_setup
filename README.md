```bash
aws s3api create-bucket \
  --bucket setup-asevvdwa12 \
  --region ap-northeast-1 \
  --create-bucket-configuration LocationConstraint=ap-northeast-1
```

```bash
aws ec2 enable-ebs-encryption-by-default --region ap-northeast-1
```


```bash
PROJECT=project.tar.gz
tar -czvf $PROJECT ./
mv $PROJECT ~/Desktop
aws s3 cp s3://setup-asevvdwa12/project.tar.gz ./
tar -xzvf project.tar.gz

```


```bash
aws organizations create-organization --feature-set ALL
```


```bash
aws iam create-user --user-name PowerUser
aws iam attach-user-policy --user-name PowerUser --policy-arn arn:aws:iam::aws:policy/PowerUserAccess
aws iam attach-user-policy --user-name PowerUser --policy-arn arn:aws:iam::aws:policy/IAMUserChangePassword

aws iam create-access-key --user-name PowerUser
aws iam create-login-profile --user-name PowerUser --password "SecurePassword123!" --password-reset-required

```

# terraform用のバケット作成
```bash
aws s3api create-bucket \
  --bucket terraform123d \
  --region ap-northeast-1 \
  --create-bucket-configuration LocationConstraint=ap-northeast-1
```

# 作業用EC2作成

## IAMロールの作成
### 信頼ポリシー (trust-policy.json) を準備

```bash
vim trust-policy.json
```

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

### IAMロール作成コマンド

```bash
aws iam create-role \
    --role-name OperationRole \
    --assume-role-policy-document file://trust-policy.json
```

### ポリシーのアタッチ
```bash
aws iam attach-role-policy \
    --role-name OperationRole \
    --policy-arn arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore

aws iam attach-role-policy \
    --role-name OperationRole \
    --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess
```

## IAMインスタンスプロファイルの作成

### インスタンスプロファイル作成

```bash
aws iam create-instance-profile \
    --instance-profile-name MyEC2InstanceProfile
```

### インスタンスプロファイルにロールを追加

```bash
aws iam add-role-to-instance-profile \
    --instance-profile-name MyEC2InstanceProfile \
    --role-name OperationRole
```

### インスタンスプロファイルにロールを追加

```bash
aws iam add-role-to-instance-profile \
    --instance-profile-name MyEC2InstanceProfile \
    --role-name OperationRole
```