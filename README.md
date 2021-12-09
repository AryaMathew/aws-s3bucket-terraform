# aws-s3bucket-using-terraform
Here its a simple document on how to use Teraform to make s3-static-website

## Prerequisites

- [AWS Access Key and Secret Key](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)
- [Install Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [Install Git](https://github.com/git-guides/install-git)

## Features
This will allows for Hosting a Static Website on Amazon S3, provisioning the following details:

- S3 Bucket for static public files.
- Uploading files from a local directory to designated s3 bucket.
- Assigning required IAM policies for the bucket.

## Usage

- Clone the repository

```html
$ git clone https://github.com/AryaMathew/aws-s3bucket-terraform.git
$ cd aws-s3bucket-terraform
$ terraform init
$ terraform apply
```


## How to configure

- Firstly we can update the region, AWS access key, secret key in the provider.tf file.Then create an S3 bucket to store the static files which is publically accessible.

 ```html
resource "aws_s3_bucket" "bucket" {
  bucket = "s3-test.aryatess.tech"
  acl    = "public-read"
  policy = file("policy.json")

   website {
    index_document = "index.html"
    error_document = "error.html"
}

  tags = {
    Name        = "s3-test.aryatess.tech"
  }
}
output "site-url" {
    value = aws_s3_bucket.bucket.website_endpoint
}

```
- Then create a IAM policy document (policy.json) to manage bucket permissions and assiging this policy to S3 bucket.

```html

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::s3-test.aryatess.tech/*"
        }
    ]
}

```
- Also we can use invoke awscli commands with null_resource provisioner.

```html
resource "null_resource" "remove_and_upload_to_s3" {
  provisioner "local-exec" {
    command = "aws s3 sync ${path.module}/s3Contents s3://${aws_s3_bucket.bucket.id}"
  }
}
```

## Result

[![image.png](https://i.postimg.cc/YC0LtM05/image.png)](https://postimg.cc/hXFtrF9s)![image](https://user-images.githubusercontent.com/94452976/145450818-57245295-5cbb-4070-80eb-5cfca97f3d2f.PNG)


