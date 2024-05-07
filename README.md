# Terraform AWS S3 Static Website Hosting

## Project Description:

This project aims to demonstrate the automated provisioning and deployment of a web hosting solution for static websites using terraform by storing in an s3 bucket. This project provides a good foundation for hosting various types of static websites like blogs, portfolios, or small scaled business websites.

## Prerequisites:
1. An AWS account
2. Good understanding  of Terraform 
3. An IDE preferably VSCode.

# Steps :
## Step 1: Set Up Your Development Environment
Ensure you have the AWS Command Line Interface (CLI) on your local machine. You can configure Configure your AWS credentials by running the command bellow  and providing your AWS access key and secret key.
```
aws configure
```

## Step 2: Define Your Website Content
Prepare your static website files (HTML) and you can choose to name the main HTML file "index.html. As a bonus, you can also include an "error.html" file.

## Step 3: Define your Configuration Files

First, you start by creating a provider file using the code below. It is good practice to name your provider configuration file as  "provider.tf"

```
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "5.48.0"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}
```

Secondly, you also create your main configuration file which could contain all the resources you intend to create. I also prefer to name the file "main.tf" 
```
resource "aws_s3_bucket" "example" {
    bucket = "my-webstatic-bucket"

}
```
Next to this are codes containing bucket policies that can be added to the main.tf file
```
resource "aws_s3_bucket_public_access_block" "example" {
    bucket = aws_s3_bucket.example.id
    block_public_acls       = false
    block_public_policy     = false
    ignore_public_acls      = false
    restrict_public_buckets = false

}

resource "aws_s3_object" "index" {
    bucket = aws_s3_bucket.example.id
    key = "index.html"
    source = "./index.html"
}

resource "aws_s3_object" "error" {
    bucket = aws_s3_bucket.example.id
    key = "error.html"
    source = "./error.html"
}

resource "aws_s3_bucket_website_configuration" "example" {
    bucket = aws_s3_bucket.example.id

    index_document {  
        suffix = "index.html"
    }

    error_document {
        key = "error.html"
    }
}

resource "aws_s3_bucket_policy" "public_read_access" {
  bucket = aws_s3_bucket.example.id
  policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
	  "Principal": "*",
      "Action": [ "s3:GetObject" ],
      "Resource": [
        "${aws_s3_bucket.example.arn}",
        "${aws_s3_bucket.example.arn}/*"
      ]
    }
  ]
}
EOF

}
```
## Step 4:  
Run the following commands simultaneously to create the resources in your AWS account.

```
terraform init
```
```
terraform plan
```
```
terraform apply
```

## Step 5: Define the Output file
An output file is needed to obtain your website link in your IDE, eliminating the need to access the link through the AWS Console. That can be achieved using the code below

```
output "websiteendpoint" {
    value = aws_s3_bucket.example.website_endpoint
}
```

Then, run the command below to effect the change and get your website link

```
terraform apply
```

It will give your website link as output as shown below.

![Screenshot (559)](https://github.com/Geewillzz/STATIC_WEBSITE_HOSTING_WITH_TERRAFORM/assets/108093831/96620f55-0953-4c4e-b83f-891caa16798a)

## Step 6: Verify the Output
Please copy the link and paste it into your desired web browser.

![Screenshot (560)](https://github.com/Geewillzz/STATIC_WEBSITE_HOSTING_WITH_TERRAFORM/assets/108093831/01b51a8e-4f81-4182-97c0-052e8069e091)


