# AUTOMATED-S3-WEB-HOSTING

 This project involves hosting a static website on AWS S3 using Terraform, with all configuration and automation coded through PowerShell—including writing Terraform files and executing commands like init, plan, and apply.

# OVERVIEW 

• infrastructure : AWS-S3 

• IaC : Terraform 

• Scripting/Automation : Powershell 



 # PROVIDER --> provider.tf

<pre>
<code>
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "6.0.0-beta3"
    }
  }
}

provider "aws" {
	region = "ap-south-1"
}
</code> 
</pre>

# VARIABLE --> variable.t
<pre>
<code>
variable "bucketname" {
	default = "bucketprojecthosting22"
}	
</code>
</pre>

# TO CREATE BUCKET --> main.tf

<pre>
<code>
resource "aws_s3_bucket" "projectbucket" {
  bucket = var.bucketname
}
</code>
</pre>

<img src="https://github.com/gowtthamm/AUTOMATED-S3-WEB-HOSTING/blob/a032d97d5f88709fb288a9525383c961532a0593/Bucket%20Created.png" width="600" height="200" />

# OWNERSHIP CONTROL --> main.tf

<pre>
<code>
resource "aws_s3_bucket_ownership_controls" "example" {
  bucket = aws_s3_bucket.projectbucket.id

  rule {
    object_ownership = "BucketOwnerPreferred"
  }
}
</code>
</pre>

# BUCKET PUBLIC ACCESS --> main.tf

<pre>
<code>
resource "aws_s3_bucket_public_access_block" "example" {
  bucket = aws_s3_bucket.projectbucket.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}
</code>
</pre>

<img src="https://github.com/gowtthamm/AUTOMATED-S3-WEB-HOSTING/blob/b8d78ebd6f937410bdb1d58309ebfed92da54216/public%20access.png" width="600" height="200"/>

# ACL FOR BUCKET TO MAKE IT PUBLIC --> main.tf

<pre>
<code>
resource "aws_s3_bucket_acl" "example" {
  depends_on = [
    aws_s3_bucket_ownership_controls.example,
    aws_s3_bucket_public_access_block.example,
  ]

  bucket = aws_s3_bucket.projectbucket.id
  acl    = "public-read"
}
</code>
</pre>


#  index.html // error.html --> main.tf
<pre>
<code>
resource "aws_s3_object" "index" {
  bucket       = aws_s3_bucket.projectbucket.id
  key          = "index.html"
  source       = "index.html"
  acl          = "public-read"
  content_type = "text/html"

  depends_on = [
    aws_s3_bucket.projectbucket,
    aws_s3_bucket_acl.example
  ]
}

resource "aws_s3_object" "error" {
  bucket       = aws_s3_bucket.projectbucket.id
  key          = "error.html"
  source       = "error.html"
  acl          = "public-read"
  content_type = "text/html"

  depends_on = [
    aws_s3_bucket.projectbucket,
    aws_s3_bucket_acl.example
  ]
}
</code>
</pre>

<img src="https://github.com/gowtthamm/AUTOMATED-S3-WEB-HOSTING/blob/3300d19ecd027ab8730de80dbff7a31d0dd9a156/Object.png" width="700" height="200" />

# WEBHOSTING CONFIGURATION --> Main.tf

<pre>
<code>
resource "aws_s3_bucket_website_configuration" "website" {
  bucket = aws_s3_bucket.projectbucket.id

  index_document {
    suffix = "index.html"
  }
  error_document {
    key = "error.html"
  }
depends_on = [aws_s3_bucket_acl.example]
}
</code>
</pre>

<img src="https://github.com/gowtthamm/AUTOMATED-S3-WEB-HOSTING/blob/209b338f98da79dcc2cd0e19088db4772e564570/weblink.png" width="600" height="200" />

# TERRAFORM COMMANDS 

1.  Initializes the working

          terraform init

<img src="https://github.com/gowtthamm/AUTOMATED-S3-WEB-HOSTING/blob/2980c712875180f19bb75522603e34cc12715147/init.png" />
     
2.  Review the infrastructure changes

         terraform plan

<img src="https://github.com/gowtthamm/AUTOMATED-S3-WEB-HOSTING/blob/dbcd8b5629c97509e3076550d63e76c29ff88c63/plan.png" />

3.  Apply the infrastructure:

          terraform apply

<img src="https://github.com/gowtthamm/AUTOMATED-S3-WEB-HOSTING/blob/471a0da09542884be0f2073352dfa6880ec5d5a9/Output.png" width="600" height="400" />


# DESTROY 

	terraform destroy 
 
<img src="https://github.com/gowtthamm/AUTOMATED-S3-WEB-HOSTING/blob/4e02e4e31f31c9c88cf619b9d6e44380d8c749b1/destroy.png" />



