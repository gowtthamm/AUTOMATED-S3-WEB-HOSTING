# AUTOMATED-S3-WEB-HOSTING

 This project involves hosting a static website on AWS S3 using Terraform, with all configuration and automation coded through PowerShell—including writing Terraform files and executing commands like init, plan, and apply.

# OVERVIEW 

• infrastructure : AWS-S3 

• IaC : Terraform 

• Scripting/Automation : Powershell 

# PROCEDURE 

--> Provider.tf :

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

        
