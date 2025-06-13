# AUTOMATED-S3-WEB-HOSTING

 This project involves hosting a static website on AWS S3 using Terraform, with all configuration and automation coded through PowerShell—including writing Terraform files and executing commands like init, plan, and apply.

# OVERVIEW 

• infrastructure : AWS-S3 

• IaC : Terraform 

• Scripting/Automation : Powershell 



 # Provider.tf 

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

# TO CREATE BUCKET --> main.tf

<pre>
<code>
resource "aws_s3_bucket" "projectbucket" {
  bucket = var.bucketname
}

resource "aws_s3_bucket" "example" {
  bucket = "terraformproject2025" 
}
</code>
</pre>

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

# HTML CODE 
<pre>
<code> 
<!DOCTYPE html>
<html>
<head>
    <title>Hello World</title>
</head>
<body>
    <h1>Hello, World!</h1>
</body>
</html>
</code>
</pre>

# ERROR HTML CODE
<pre>
<code>
<!DOCTYPE html>
<htm> <!-- Typo: should be <html> -->
  <head>
    <titel>Hello</titel> <!-- Typo: should be <title> -->
    <script>
      // Intentional JavaScript error
      console.log(myUndefinedVariable); // ReferenceError
    </script>
  </head>
  <body>
    <h1>Hello, World! <!-- Missing closing tag -->
    <p>This is a broken HTML file.</p>
  </body>
</htm>

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
