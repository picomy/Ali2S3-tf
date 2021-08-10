[toc]

# Ali2S3 module introduction

This module will replicate the data from Ali cloud object storage to Amazon S3.

## Which tools are involed
- Terraform
- rclone
- Amazon ECS
- Amazon SecretsManager
- Python
- Docker
  
## Security
Your sensitive information will be saved into SecretsManager. 
- access key
- access secret key

## Terraform module structure
- Ali2S3.tf, main module which integrates all block together.
- variables.tf, define the variables which you need to provide for this module.
- Ali2S3-Docker, which includes the ECR images defination.
- Ali2S3.conf, sample conf.

## Depyloment
![](./img/deployment.png)


## How to use this module

### Step 1
- Dataset name
- Source
  - Ali Storage Account Access Key
  - Ali Storage Account Access Secret Key
- Destination 
  - S3 bucket name
  - KMS arn
  - access_key
  - secret_access_key
  - region

### Step 2
Download the [terraform](https://www.terraform.io/downloads.html)

### Step 3
Create the directory to include your terraform file.
```hcl
module "Ali2S3" {
  source = "/Users/picomy/Playground/Ali2S3-tf"

  DataSetName = "dataset-5"
  
  vpcID          = "vpc-0d680669"
  subnetIDs      = ["subnet-eabc819d", "subnet-17388b73"]
  securityGroups = ["sg-0b70322a890ba7ef1"]
}
```

Copy the Ali2S3.conf from Ali2S3-tf into your current directory.

### Step 4

The Ali2S3.conf defines the cross border data transfer.
```ini
[oss]
type = s3
provider = Alibaba
env_auth = false
access_key_id = 
secret_access_key = 
endpoint = oss-cn-beijing.aliyuncs.com
acl = private
storage_class = Standard

[s3]
type = s3
provider = AWS
env_auth = false
access_key_id =
secret_access_key =
region = cn-north-1
endpoint = s3.cn-north-1.amazonaws.com.cn
location_constraint = cn-north-1
acl = bucket-owner-full-control
server_side_encryption = 
sse_kms_key_id = 

[Replication]
src_path = oss:cross-cloud-
dst_path = s3:picoman-dest-2
```

### Step 5 

```shell
export AWS_DEFAULT_REGION=cn-north-1
terraform init
terraform plan
terraform apply
```

### Step 6
Login to your cloud account, and access the Secrets Manager, you will see the following secrets:
- cbdt-{datasetname}-secret-ali2s3

update your src and dest s3 aksk.


## FAQ

### How to change the ingest frequency?
Login to your cloud account, and access the Cloudwatch/Events/Rule/{datasetname}-Trigger-SFTP2S3-Rule,change the setting.

### Whether does it support accessing cross the account?
Yes.

### Whether does it support bi-directional data transfer?
Yes.
# Ali2S3-tf
