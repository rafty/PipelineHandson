# CloudFormation Pipeline Handson

# 前提

- AWS Account
  > region: ap-northeast-1を使用する

- AWS CLI
- Pycharm  
  - [Pycharm Communityインストール](https://www.jetbrains.com/pycharm/download/#section=mac)
  > - Plugin: AWS Cloudformation 
  > - Pipenv python 3.7.3
- Github Repository  
  > - OAuthToken

- AWS  
  > EC2 KeyPairの作成

# Cloudformation練習

![](./images/automation.svg)    
![](./images/stack-sam.svg)  



1.   

```
aws cloudformation create-stack \
--stack-name cfn-practice \
--region ap-northeast-1 \
--template-body file://cfn-practice/practice-1.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=cfn-prctc \
ParameterKey=Stage,ParameterValue=dev \
ParameterKey=VPCCidrBlock,ParameterValue=10.0.0.0/16 \
```

2.  

```
aws cloudformation update-stack \
--stack-name cfn-practice \
--region ap-northeast-1 \
--template-body file://cfn-practice/practice-2.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=cfn-prctc \
ParameterKey=Stage,ParameterValue=dev \
ParameterKey=VPCCidrBlock,ParameterValue=10.0.0.0/16 \
```

3.  

```
aws cloudformation update-stack \
--stack-name cfn-practice \
--region ap-northeast-1 \
--template-body file://cfn-practice/practice-3.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=cfn-prctc \
ParameterKey=Stage,ParameterValue=dev \
ParameterKey=VPCCidrBlock,ParameterValue=10.0.0.0/16 \
```

4.  

```
aws cloudformation update-stack \
--stack-name cfn-practice \
--region ap-northeast-1 \
--template-body file://cfn-practice/practice-4.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=cfn-prctc \
ParameterKey=Stage,ParameterValue=dev \
ParameterKey=VPCCidrBlock,ParameterValue=10.0.0.0/16 \
```

5.  
```
aws cloudformation update-stack \
--stack-name cfn-practice \
--region ap-northeast-1 \
--template-body file://cfn-practice/practice-5.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=cfn-prctc \
ParameterKey=Stage,ParameterValue=dev \
ParameterKey=VPCCidrBlock,ParameterValue=10.0.0.0/16 \
```

----


```
# KeyPairの確認 無ければ作成
$ aws ec2 describe-key-pairs
```

```
# ネストテンプレートをpackageしてuploadする
$ aws cloudformation package \
--template-file master-stack.yml \
--s3-bucket rafty-test \
--output-template-file /tmp/test-packed.yml

$ aws cloudformation deploy \
--template-file /tmp/test-packed.yml \
--stack-name ecs-rsc-stack \
--capabilities CAPABILITY_NAMED_IAM \
--parameter-overrides \
System=ecs-rsc \
Stage=dev \
VPCCidrBlock=10.0.0.0/16 \
InstanceType=t3.nano \
KeyName=kdc-poc
```

---
> 以下は参考

```
$ aws cloudformation create-stack \
--stack-name ecs-rsc-pipeline \
--region ap-northeast-1 \
--template-body file://master-stack.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=ecs-rsc \
ParameterKey=Stage,ParameterValue=dev \
ParameterKey=VPCCidrBlock,ParameterValue=10.0.0.0/16 \
ParameterKey=InstanceType,ParameterValue=t3.nano \
ParameterKey=KeyName,ParameterValue=kdc-poc
```

```
$ aws cloudformation update-stack \
--stack-name ecs-rsc-pipeline \
--region ap-northeast-1 \
--template-body file://master-stack.yml \
--capabilities CAPABILITY_NAMED_IAM
--parameters \
ParameterKey=System,ParameterValue=ecs-rsc \
ParameterKey=Stage,ParameterValue=dev \
ParameterKey=VPCCidrBlock,ParameterValue=10.0.0.0/16 \
ParameterKey=InstanceType,ParameterValue=t3.nano \
ParameterKey=KeyName,ParameterValue=kdc-poc
```

# pipelineの実行

![](./images/pipelilne-sam.svg)  

```
aws cloudformation create-stack \
--stack-name ecs-rsc-pipeline \
--region ap-northeast-1 \
--template-body file://pipeline/pipeline.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=ecs-rsc
```

```
aws cloudformation update-stack \
--stack-name ecs-rsc-pipeline \
--region ap-northeast-1 \
--template-body file://pipeline/pipeline.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=ecs-rsc
```

# Stackの削除
練習が終わったらprd,stg,dev stackを削除する手順  
1. prd/stg/dev stackを削除  
2. codepipelineで作成したS3 Bucketをコンソールから削除する  
3. codepipelineのstackを削除する  

もし以下のようなエラーが発生したら、ecs-rsc-pipeline-CFnRole-M7BGEV8E9AZ0というRoleを新規に作成する。  
このロールを使用するサービスを選択 -> cloudformationを選択  
Attach アクセス権限ポリシー -> AdministratorAccessを選択  
-> 作成
もう一度Stackを削除する  
```
Role arn:aws:iam::338456725408:role/ecs-rsc-pipeline-CFnRole-M7BGEV8E9AZ0 is invalid or cannot be assumed
```
__参考__  
[StackOverflow](https://stackoverflow.com/questions/48709423/unable-to-delete-cfn-stack-role-is-invalid-or-cannot-be-assumed)  
[Pipeline スターターキットで、AWS 上での継続的デリバリーをお試し](https://aws.typepad.com/sajp/2016/04/explore-continuous-delivery-in-aws-with-the-pipeline-starter-kit.html)  


# Pipeline概説

![](./images/infra-pipeline1.svg)  
![](./images/infra-pipeline2.svg)  
![](./images/infra-pipeline3.svg)  


# 参考
[Continuous Delivery of Nested AWS CloudFormation Stacks Using AWS CodePipeline](https://aws.amazon.com/jp/blogs/devops/continuous-delivery-of-nested-aws-cloudformation-stacks-using-aws-codepipeline/)  
![](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2017/06/07/Pipeline_vertical_design-2-362x1024.png)  
[GitHub/CodeBuild/CodePipelineを利用してCloudFormationのCI/CDパイプラインを構築する](https://dev.classmethod.jp/cloud/aws/developing-cloudformation-ci-cd-pipeline-with-github-codebuild-codepipeline/)
