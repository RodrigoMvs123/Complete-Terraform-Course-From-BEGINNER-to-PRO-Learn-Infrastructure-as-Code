# Complete-Terraform-Course-From-BEGINNER-to-PRO-Learn-Infrastructure-as-Code

https://www.youtube.com/watch?v=7xngnjfIlK4 

https://raw.githubusercontent.com/RodrigoMvs123/Complete-Terraform-Course-From-BEGINNER-to-PRO-Learn-Infrastructure-as-Code/main/README.md

https://github.com/RodrigoMvs123/Complete-Terraform-Course-From-BEGINNER-to-PRO-Learn-Infrastructure-as-Code/blame/main/README.md

https://github.com/sidpalas/devops-directive-terraform-course 

1 - Evolution of Cloud + Infrastructure as Code
2 - Terraform Overview +  Setup
3 - Basic Terraform Usage
4 - Variables and Outputs
5 - Language Features 
6 - ProjectOrganization + Modules
7 - Managing Multiple Environments
8 - Testing Terraform Code 
9 - Developer Workflows

Reference Architecture 

Basic Web Application 
Infrastructure all within AWS
Multiple instances running on EC2
Using default VPC for simplicity 

Amazon Route 53
Elastic Load Balancing 
Compute
Amazon EC2 - Amazon Simple Storage (S3)
Database
Amazon RDS

Party 1 
Evolution of Cloud + Infrastructure as Code

Idea
1999 to 2000
Write the software
Setup a datacenter ( Power management, Networking, Operational Overhead )

2010 to now 
Idea
Write software
Deploy to cloud

Infrastructure provisioned via API´s
Servers created and destroyed in seconds
Long-lived + multiple -> Short lived + immutable

Provisioning Cloud Resources 
Three Approuches

GUI
API/CLI
Infrastructure as Code ( IaC )

Categories of IaC tools:
Ad hoc scripts 
Configuration management tools
Server Templating tools
Orchestration tools ( Kubernetes ) 
Provisioning tools

IaC Provisioning Tools Landscape
Cloud Specific 
Cloud Formation
Azure Resource Manager
Google Cloud Deployment Manager 

Cloud Agnostic 
Terraform 
Pulumi

Party 2 
Terraform Overview +  Setup

What is Terraform
Terraform is a tool for building, changing and versioning infrastructure safely and efficiently
Enables application software best practices to infrastructure 
Compatible with many cloud and services 

Common Patterns 

Terraform
Provisioning 

Ansible
Config Management 

Server Templating 

Kubernetes 
Orchestration

Terraform Core ( Engine )
Terraform State
Terraform Config
AWS Provider 
Cloudflare Provider 
AWS
Cloudflare

Download Terraform
https://www.youtube.com/watch?v=bSrV1Dr8py8 
https://developer.hashicorp.com/terraform/downloads 
Este Computador ( Right Click )
Configurações avançadas do sistema
Variáveis de Ambientes
Path 
Editar
Novo
C:\Users\Matheus\Desktop\Rodrigo\Visual Studio Code\Terraform
Ok
Ok
PowerShell CLI
terraform - -help
terraform - -version 

AWS UI
AWS IAM 
Add User
terraform-user
User Groups
terraform-course
Permission policies
AmazonRDSFullAccess
AmazonEC2FullAccess
IAMFullAccess
AmazonS3FullAccess
AmazonDynamoDBFullAccess
AmazonRouter53FullAccess

Instal AWS CLI
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html 
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html 
CMD CLI
aws
aws config 
Access key ID
Secret access key

Visual Studio Code
Terminal
cd 02-overview
terraform init
terraform plan
terraform apply

AWS UI
EC2
Instance ( Pending )

Visual Studio Code
EXPLORER 
OPEN EDITORS 
main.tf

main.tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-011899242bb902164" # Ubuntu 20.04 LTS // us-east-1
  instance_type = "t2.micro"
}

Visual Studio Code
Terminal
terraform destroy

AWS UI
EC2
Instance ( Destroyed )

Party 3
Basic Terraform Usage

Basic Usage Sequence
terraform init
terraform plan
terraform apply
terraform destroy

Terraform Init

$ tree -a
main.tf
Terraform init
Working Directory 

$ tree -a
	terraform
		providers 
			registry.terraform.io
				hashicorp
					aws
						3.23.0
							darwin_amd64
								terraform-provider-aws_v3.23.0_x5

terraform.lock.hcl
main.tf

$ tree -a
	terraform
		modules
			modules.json
			vpc
…

State File
Terraform´s representation of the world
JSON file containing information about every resource and data object
Contain sensitive information (e.g. database password)
Can be stored locally or remotely

Local Backend

terraform state
Simple to get started
Sensitive values in plain text
Uncollaborative
Manual

Remote Backend

User
Sensitive data encrypted
Collaboration possible
Automation possible
Increased complexity

Terraform State
Terraform Cloud
Amazon Simple Storage Service (S3)

Terraform Plan

Terraform config ( Desired state )
Network Configuration
Servers ( 4 )
Database

Terraform state ( Actual state )
Network Configuration
Servers ( 3 )
Database

Plan: +1 Virtual Machine 
terraform apply 
AWS Provider

Terraform Destroy

Terraform config ( Desired state )
Network Configuration
Servers ( 4 )
Database

Plan: Destroy Everything

Remote Backend ( Terraform Cloud )

Visual Studio Code
EXPLORER 
OPEN EDITORS 
terraform-cloud-backend
main.tf

main.tf
terraform {
  backend "remote" {
    organization = "devops-directive"

    workspaces {
      name = "devops-directive-terraform-course"
    }
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

Open Terraform Cloud
https://app.terraform.io/public/signup/account 

devops-directive
Terraform Course

Visual Studio Code
EXPLORER 
OPEN EDITORS 
web-app
main.tf

main.tf
terraform {
  # Assumes s3 bucket and dynamo DB table already set up
  # See /code/03-basics/aws-backend
  backend "s3" {
    bucket         = "devops-directive-tf-state"
    key            = "03-basics/web-app/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-locking"
    encrypt        = true
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

S3 Bucket used for storage
DynamoDB used for locking

Remote Backend (AWS)
Bootstrapping - part 1
No Remote Backend Specified (defaults to local)

Visual Studio Code
EXPLORER 
OPEN EDITORS 
web-app
main.tf

main.tf
terraform
required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

Versioned and encrypted S3 Bucket

Visual Studio Code
EXPLORER 
OPEN EDITORS 
web-app
main.tf

main.tf
resource "aws_s3_bucket" "bucket" {
  bucket_prefix = "devops-directive-web-app-data"
  force_destroy = true
}

DynamoDB Table with hash_key = “LockID”

resource "aws_dynamodb_table" "terraform_locks" {
  name         = "terraform-state-locking"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"
  attribute {
    name = "LockID"
    type = "S"
  }
}

Bootstrapping - part 2
Visual Studio Code
Terminal
terraform apply
terraform init

AWS UI
terraform.tfstate
Version ID
…
…

AWS UI ( Remote Backend )

Amazon S3 / devops-directive-tf-state
DynamoDB / terraform-state-locking

Visual Studio Code
EXPLORER 
OPEN EDITORS 
web-app
main.tf

main.tf
terraform {
  # Assumes s3 bucket and dynamo DB table already set up
  # See /code/03-basics/aws-backend
  backend "s3" {
    bucket         = "devops-directive-tf-state"
    key            = "03-basics/web-app/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-locking"
    encrypt        = true
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "instance_1" {
  ami             = "ami-011899242bb902164" # Ubuntu 20.04 LTS // us-east-1
  instance_type   = "t2.micro"
  security_groups = [aws_security_group.instances.name]
  user_data       = <<-EOF
              #!/bin/bash
              echo "Hello, World 1" > index.html
              python3 -m http.server 8080 &
              EOF
}

resource "aws_instance" "instance_2" {
  ami             = "ami-011899242bb902164" # Ubuntu 20.04 LTS // us-east-1
  instance_type   = "t2.micro"
  security_groups = [aws_security_group.instances.name]
  user_data       = <<-EOF
              #!/bin/bash
              echo "Hello, World 2" > index.html
              python3 -m http.server 8080 &
              EOF
}

resource "aws_s3_bucket" "bucket" {
  bucket_prefix = "devops-directive-web-app-data"
  force_destroy = true
}

resource "aws_s3_bucket_versioning" "bucket_versioning" {
  bucket = aws_s3_bucket.bucket.id
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_s3_bucket_server_side_encryption_configuration" "bucket_crypto_conf" {
  bucket = aws_s3_bucket.bucket.bucket
  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}

data "aws_vpc" "default_vpc" {
  default = true
}

data "aws_subnet_ids" "default_subnet" {
  vpc_id = data.aws_vpc.default_vpc.id
}

resource "aws_security_group" "instances" {
  name = "instance-security-group"
}

resource "aws_security_group_rule" "allow_http_inbound" {
  type              = "ingress"
  security_group_id = aws_security_group.instances.id

  from_port   = 8080
  to_port     = 8080
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
}

resource "aws_lb_listener" "http" {
  load_balancer_arn = aws_lb.load_balancer.arn

  port = 80

  protocol = "HTTP"

  # By default, return a simple 404 page
  default_action {
    type = "fixed-response"

    fixed_response {
      content_type = "text/plain"
      message_body = "404: page not found"
      status_code  = 404
    }
  }
}

resource "aws_lb_target_group" "instances" {
  name     = "example-target-group"
  port     = 8080
  protocol = "HTTP"
  vpc_id   = data.aws_vpc.default_vpc.id

  health_check {
    path                = "/"
    protocol            = "HTTP"
    matcher             = "200"
    interval            = 15
    timeout             = 3
    healthy_threshold   = 2
    unhealthy_threshold = 2
  }
}

resource "aws_lb_target_group_attachment" "instance_1" {
  target_group_arn = aws_lb_target_group.instances.arn
  target_id        = aws_instance.instance_1.id
  port             = 8080
}

resource "aws_lb_target_group_attachment" "instance_2" {
  target_group_arn = aws_lb_target_group.instances.arn
  target_id        = aws_instance.instance_2.id
  port             = 8080
}

resource "aws_lb_listener_rule" "instances" {
  listener_arn = aws_lb_listener.http.arn
  priority     = 100

  condition {
    path_pattern {
      values = ["*"]
    }
  }

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.instances.arn
  }
}


resource "aws_security_group" "alb" {
  name = "alb-security-group"
}

resource "aws_security_group_rule" "allow_alb_http_inbound" {
  type              = "ingress"
  security_group_id = aws_security_group.alb.id

  from_port   = 80
  to_port     = 80
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]

}

resource "aws_security_group_rule" "allow_alb_all_outbound" {
  type              = "egress"
  security_group_id = aws_security_group.alb.id

  from_port   = 0
  to_port     = 0
  protocol    = "-1"
  cidr_blocks = ["0.0.0.0/0"]

}


resource "aws_lb" "load_balancer" {
  name               = "web-app-lb"
  load_balancer_type = "application"
  subnets            = data.aws_subnet_ids.default_subnet.ids
  security_groups    = [aws_security_group.alb.id]

}

resource "aws_route53_zone" "primary" {
  name = "devopsdeployed.com"
}

resource "aws_route53_record" "root" {
  zone_id = aws_route53_zone.primary.zone_id
  name    = "devopsdeployed.com"
  type    = "A"

  alias {
    name                   = aws_lb.load_balancer.dns_name
    zone_id                = aws_lb.load_balancer.zone_id
    evaluate_target_health = true
  }
}

resource "aws_db_instance" "db_instance" {
  allocated_storage = 20
  # This allows any minor version within the major engine_version
  # defined below, but will also result in allowing AWS to auto
  # upgrade the minor version of your DB. This may be too risky
  # in a real production environment.
  auto_minor_version_upgrade = true
  storage_type               = "standard"
  engine                     = "postgres"
  engine_version             = "12"
  instance_class             = "db.t2.micro"
  name                       = "mydb"
  username                   = "foo"
  password                   = "foobarbaz"
  skip_final_snapshot        = true
}

Visual Studio Code
Terminal
cd web-app
terraform init
terraform plan
terraform apply

AWS - UI
EC2 
Instances
load balancers ( EC2 feature )

Route S3 ( devopsdeployed.com )
Hosted zone details ( Name servers )
DNS
Google Domains ( Name server )

Load Balancer
web-app-lb
DNS name … ( Hello, World 1 or 2 )

RDS ( Relational Database Service )
DB Identifier 

Visual Studio Code
Terminal
terraform destroy

Party 4 
Variables and Outputs 

Variables Types 
input variable
var.<name>
variable “intance_type”{
description…
…

Local variables
local.<name>
locals {
service_name …
…

Output variables 
output “instance_ip_addr” {
value …
…

Setting Input Variables
( In order of precedence // lowest -> highest )
Manual entry during plan/apply
Default value in declaration block
TF_VAR_<name> environment variables
terraform.tfvars file
*.auto.tfvars file
Command line -var or -var-file

Types and Validation 
Primitive Types:
string
number
bool

Complex Types
list(<TYPE>)
set(<TYPE>)
map(<TYPE>)
object({<ATTR NAME>=<TYPE>,...})
tuple([<TYPE>,...])

Validation:
Type checking happens automatically 
Custom conditions can also be enforced 

Sensitive Data
Mark variables as sensitive:
Sensitive = true

Pass to terraform apply with:
TV_VAR_variable
-var ( retrived from secret manager at runtime )

Can also use external secret store
For example, AWS secret manager


Visual Studio Code
EXPLORER 
OPEN EDITORS 
variables-and-output
examples
main.tf

main.tf
terraform {
  backend "s3" {
    bucket         = "devops-directive-tf-state"
    key            = "04-variables-and-outputs/examples/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-locking"
    encrypt        = true
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

locals {
  extra_tag = "extra-tag"
}

resource "aws_instance" "instance" {
  ami           = var.ami
  instance_type = var.instance_type

  tags = {
    Name     = var.instance_name
    ExtraTag = local.extra_tag
  }
}

resource "aws_db_instance" "db_instance" {
  allocated_storage   = 20
  storage_type        = "gp2"
  engine              = "postgres"
  engine_version      = "12"
  instance_class      = "db.t2.micro"
  name                = "mydb"
  username            = var.db_user
  password            = var.db_pass
  skip_final_snapshot = true
}

Visual Studio Code
Terminal
terraform apply -var=“db_user=myuser” -var=“db_pass=SOMETHINGSUPERSECURE”

Visual Studio Code
EXPLORER 
OPEN EDITORS 
variables-and-output
examples
main.tf
variables.tf

variables.tf
# should specify optional vs required

variable "instance_name" {
  description = "Name of ec2 instance"
  type        = string
}

variable "ami" {
  description = "Amazon machine image to use for ec2 instance"
  type        = string
  default     = "ami-011899242bb902164" # Ubuntu 20.04 LTS // us-east-1
}

variable "instance_type" {
  description = "ec2 instance type"
  type        = string
  default     = "t2.micro"
}

variable "db_user" {
  description = "username for database"
  type        = string
  default     = "foo"
}

variable "db_pass" {
  description = "password for database"
  type        = string
  sensitive   = true
}

Visual Studio Code
EXPLORER 
OPEN EDITORS 
variables-and-output
examples
main.tf
terraformtf.vars

terraformtf.vars
instance_name = "hello-world"
ami           = "ami-011899242bb902164" # Ubuntu 20.04 LTS // us-east-1
instance_type = "t2.micro"

Visual Studio Code
EXPLORER 
OPEN EDITORS 
variables-and-output
examples
main.tf
another-variable-file.tfvars

another-variable-file.tfvars
instance_name = "hello-world-2"

Visual Studio Code
Terminal
terraform apply -var-file=another-variable-file.tfvars

Visual Studio Code
EXPLORER 
OPEN EDITORS 
variables-and-output
examples
main.tf
outputs.tf

outputs.tf
output "instance_ip_addr" {
  value = aws_instance.instance.private_ip
}

output "db_instance_addr" {
  value = aws_db_instance.db_instance.address
}

Visual Studio Code
EXPLORER 
OPEN EDITORS 
variables-and-output
examples
web-app
variable.tf

variables.tf
# General Variables

variable "region" {
  description = "Default region for provider"
  type        = string
  default     = "us-east-1"
}

# EC2 Variables

variable "ami" {
  description = "Amazon machine image to use for ec2 instance"
  type        = string
  default     = "ami-011899242bb902164" # Ubuntu 20.04 LTS // us-east-1
}

variable "instance_type" {
  description = "ec2 instance type"
  type        = string
  default     = "t2.micro"
}

# S3 Variables

variable "bucket_prefix" {
  description = "prefix of s3 bucket for app data"
  type        = string
}

# Route 53 Variables

variable "domain" {
  description = "Domain for website"
  type        = string
}

# RDS Variables

variable "db_name" {
  description = "Name of DB"
  type        = string
}

variable "db_user" {
  description = "Username for DB"
  type        = string
}

variable "db_pass" {
  description = "Password for DB"
  type        = string
  sensitive   = true
}

Visual Studio Code
EXPLORER 
OPEN EDITORS 
variables-and-output
examples
web-app
outputs.tf

outputs.tf
output "instance_1_ip_addr" {
  value = aws_instance.instance_1.public_ip
}

output "instance_2_ip_addr" {
  value = aws_instance.instance_2.public_ip
}

output "db_instance_addr" {
  value = aws_db_instance.db_instance.address
}

Visual Studio Code
EXPLORER 
OPEN EDITORS 
variables-and-output
examples
web-app
terraform.tfvars

terraform.tfvars
bucket_prefix = "devops-directive-web-app-data"
domain        = "devopsdeployed.com"
db_name       = "mydb"
db_user       = "foo"
# db_pass = "foobarbaz"

Party 5
Additional Language Features 

Expressions + Functions 
## Expressions

### Strings
```py
"foo" # literal string

"foo ${var.bar}" # template string
```

### Operators
```py
# Order of operations: 
!, - # (multiplication by -1)
*, /, % # (modulo)
+, - # (subtraction)
>, >=, <, <= # (comparison)
==, != # (equality)
&& # (AND)
|| # (OR)
```

### Conditionals

Ternary syntax can be used to conditionally set values based on other values.

```py
condition ? true_val : false_val

# For example
var.a != "" ? var.a : "default-a"
```

### Other expression types:
- For expressions
- Splat expressions
- Dynamic blocks
- Type constraints
- Version constraints

## Functions
```py
# Numeric
abs()
ceil()
floor()
log()
max()
parseint() # parse as integer
pow()
signum() # sign of number

# string
chomp() # remove newlines at end
format() # format number
formatlist()
indent()
join()
lower()
regex()
regexall()
replace()
split()
strrev() # reverse string
substr()
title()
trim()
trimprefix()
trimsuffix()
trimspace()
upper()
```
### Other function types:
- Colleciton
- Encoding
- Filesystem
- Date & Time
- Hash & Crypto
- IP Network
- Type Conversion

## Meta-arguments

Special arguments to control the behavior of resources and/or modules

### depends_on

Allows specifying dependencies which do not manifest directly through consumption of data from another resource. For example if the creation order of two resources matters, the latter can be specified to depend on the former.

### count

Allows for creation of multiple of a particular resource or module. This is most useful if each instance configuration is nearly identical.

`count.index` can be referenced for each resource.

`Count = 0` can also be used to prevent creation of a resource or modules. This is usually used in conjunction with conditional expression to selectively determine if the resource needs to be created.

### for_each

Also allows for multiple of a particular res
ource or module but allows for more control across the instances by iterating over a list.

```json
resource "some_resource" "example" {
  for_each = toset( ["foo", "bar", "baz"] )
  name     = each.key
}
```

This would create three copies of `some_resource` with the name set to `"foo"`, `"bar"`, and `"baz"` respectively

### lifecycle

Lifecycle meta-arguments control how Terraform treats particular resources.

#### create_before_destroy

Specifying `create_before_destroy = true` indicates that if the resource does need to be destroyed, Terraform should first provision its replacement before destroying the deprecated resource. This can be useful for things such as zero downtime deployments.

```json
resource "some_resource" "example" {
  # ...

  lifecycle {
    create_before_destroy = true
  }
}
```

#### ignore_changes

Sometimes an entity outside of terraform will automatically modify a resource (e.g. adding metadata, etc...). The `ignore_changes` argument allows you to ignore specific types of resource changes to prevent this from causing Terraform to attempt to revert those changes.

#### prevent_destroy

`prevent_destroy` provides an additional stopgap against accidentally destroying resources with terraform. If set to true, Terraform will reject any attempt to destroy that resource.

Provisioners
Perform action on local or remote machine

file
local-exec
remote-exec
vendor
chef
puppet

Terraform +

Ansible
Chef
Puppet

https://developer.hashicorp.com/terraform/language 

Party 6
Project Organization + Modules

Types of Modules
Module Sources 
Inputs + Meta-Arguments 

What makes a good module
Raises abstraction level from base resource types
Groups resources in a logical fashion 
Exposes input variables to allow necessary customization + composition
Provides useful defaults
Return outputs to make further integrations possible

Terraform Registry
Ex: 
terraform-aws-modules / security group
AWS EC2 - VPC Security Group Terraform Module

AWS Consul

Visual Studio Code
EXPLORER 
OPEN EDITORS 
organization-and-modules
consul
main.tf

main.tf
terraform {
  # Assumes s3 bucket and dynamo DB table already set up
  # See /code/03-basics/aws-backend
  backend "s3" {
    bucket         = "devops-directive-tf-state"
    key            = "06-organization-and-modules/consul/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-locking"
    encrypt        = true
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

############################################################
##
## NOTE: if you are deploying this in your production setup
## follow the instructions in the github repo on how to modify
## deploying with the defaults here as an example of the power
## of modules.
##
## REPO: https://github.com/hashicorp/terraform-aws-consul
##
############################################################
module "consul" {
  source = "git@github.com:hashicorp/terraform-aws-consul.git"
}

Visual Studio Code
Terminal
cd ../../06-organization-and-modules
ls
cd consul/
terraform init
terraform plan 

Visual Studio Code
EXPLORER 
OPEN EDITORS 
organization-and-modules
web-app-application
variables.tf

variables.tf
# General Variables

variable "region" {
  description = "Default region for provider"
  type        = string
  default     = "us-east-1"
}

variable "app_name" {
  description = "Name of the web application"
  type        = string
  default     = "web-app"
}

variable "environment_name" {
  description = "Deployment environment (dev/staging/production)"
  type        = string
  default     = "dev"
}

# EC2 Variables

variable "ami" {
  description = "Amazon machine image to use for ec2 instance"
  type        = string
  default     = "ami-011899242bb902164" # Ubuntu 20.04 LTS // us-east-1
}

variable "instance_type" {
  description = "ec2 instance type"
  type        = string
  default     = "t2.micro"
}

# S3 Variables

variable "bucket_prefix" {
  description = "prefix of s3 bucket for app data"
  type        = string
}

# Route 53 Variables

variable "create_dns_zone" {
  description = "If true, create new route53 zone, if false read existing route53 zone"
  type        = bool
  default     = false
}

variable "domain" {
  description = "Domain for website"
  type        = string
}

# RDS Variables

variable "db_name" {
  description = "Name of DB"
  type        = string
}

variable "db_user" {
  description = "Username for DB"
  type        = string
}

variable "db_pass" {
  description = "Password for DB"
  type        = string
  sensitive   = true
}

Visual Studio Code
EXPLORER 
OPEN EDITORS 
organization-and-modules
web-app-application
main.tf

main.tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

Visual Studio Code
EXPLORER 
OPEN EDITORS 
organization-and-modules
web-app
main.tf

main.tf
terraform {
  # Assumes s3 bucket and dynamo DB table already set up
  # See /code/03-basics/aws-backend
  backend "s3" {
    bucket         = "devops-directive-tf-state"
    key            = "06-organization-and-modules/web-app/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-locking"
    encrypt        = true
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

variable "db_pass_1" {
  description = "password for database #1"
  type        = string
  sensitive   = true
}

variable "db_pass_2" {
  description = "password for database #2"
  type        = string
  sensitive   = true
}

module "web_app_1" {
  source = "../web-app-module"

  # Input Variables
  bucket_prefix    = "web-app-1-data"
  domain           = "devopsdeployed.com"
  app_name         = "web-app-1"
  environment_name = "production"
  instance_type    = "t2.micro"
  create_dns_zone  = true
  db_name          = "webapp1db"
  db_user          = "foo"
  db_pass          = var.db_pass_1
}

module "web_app_2" {
  source = "../web-app-module"

  # Input Variables
  bucket_prefix    = "web-app-2-data"
  domain           = "anotherdevopsdeployed.com"
  app_name         = "web-app-2"
  environment_name = "production"
  instance_type    = "t2.micro"
  create_dns_zone  = true
  db_name          = "webapp2db"
  db_user          = "bar"
  db_pass          = var.db_pass_2
}

Visual Studio Code
Terminal
cd ..
cd web-app
terraform init
terraform apply
terraform destroy

Party 7
Managing Multiple Environments

One Config - Multiple Environments 

web-app ( terraform ) 

Amazon Route 53
Elastic Load Balancing 
Compute
Amazon EC2 - Amazon Simple Storage (S3)
Database
Amazon RDS

Amazon Route 53
Elastic Load Balancing 
Compute
Amazon EC2 - Amazon Simple Storage (S3)
Database
Amazon RDS

Amazon Route 53
Elastic Load Balancing 
Compute
Amazon EC2 - Amazon Simple Storage (S3)
Database
Amazon RDS

Two main approaches

Workspaces
File Structure 

Terragrunt
https://terragrunt.gruntwork.io/ 

Visual Studio Code
EXPLORER 
OPEN EDITORS 
managing-multiple-environments
workspaces
main.tf

main.tf
terraform {
  # Assumes s3 bucket and dynamo DB table already set up
  # See /code/03-basics/aws-backend
  backend "s3" {
    bucket         = "devops-directive-tf-state"
    key            = "07-managing-multiple-environments/workspaces/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-locking"
    encrypt        = true
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

variable "db_pass" {
  description = "password for database"
  type        = string
  sensitive   = true
}

locals {
  environment_name = terraform.workspace
}

module "web_app" {
  source = "../../06-organization-and-modules/web-app-module"

  # Input Variables
  bucket_prefix    = "web-app-data-${local.environment_name}"
  domain           = "devopsdeployed.com"
  environment_name = local.environment_name
  instance_type    = "t2.micro"
  create_dns_zone  = terraform.workspace == "production" ? true : false
  db_name          = "${local.environment_name}mydb"
  db_user          = "foo"
  db_pass          = var.db_pass
}

Visual Studio Code
Terminal
terraform init
terraform workspace list
terraform workspace new production
terraform workspace list
terraform apply
terraform workspace new staging 
terraform workspace list
terraform apply
terraform destroy
terraform workspace select production
terraform destroy

cd../file-structure 

Visual Studio Code
EXPLORER 
OPEN EDITORS 
managing-multiple-environments
global
main.tf

main.tf
terraform {
  # Assumes s3 bucket and dynamo DB table already set up
  # See /code/03-basics/aws-backend
  backend "s3" {
    bucket         = "devops-directive-tf-state"
    key            = "07-managing-multiple-environments/global/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-locking"
    encrypt        = true
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

# Route53 zone is shared across staging and production
resource "aws_route53_zone" "primary" {
  name = "devopsdeployed.com"
}


Visual Studio Code
Terminal
cd global/
terraform init
terraform apply

Visual Studio Code
EXPLORER 
OPEN EDITORS 
managing-multiple-environments
production
main.tf

main.tf
terraform {
  # Assumes s3 bucket and dynamo DB table already set up
  # See /code/03-basics/aws-backend
  backend "s3" {
    bucket         = "devops-directive-tf-state"
    key            = "07-managing-multiple-environments/production/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-locking"
    encrypt        = true
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

variable "db_pass" {
  description = "password for database"
  type        = string
  sensitive   = true
}

locals {
  environment_name = "production"
}

module "web_app" {
  source = "../../../06-organization-and-modules/web-app-module"

  # Input Variables
  bucket_prefix    = "web-app-data-${local.environment_name}"
  domain           = "devopsdeployed.com"
  environment_name = local.environment_name
  instance_type    = "t2.micro"
  create_dns_zone  = false
  db_name          = "${local.environment_name}mydb"
  db_user          = "foo"
  db_pass          = var.db_pass
}

Visual Studio Code
Terminal
cd ../production/
ls
terraform init
cd ..
tree

Party 8
Testing Terraform Code

Code Rot
Static Checks
Built in
External
Manual Testing
Automated Testing

Visual Studio Code
EXPLORER 
OPEN EDITORS 
testing
modules
hello-world
instance.tf

instance.tf
resource "aws_instance" "instance" {
  ami             = "ami-011899242bb902164" # Ubuntu 20.04 LTS // us-east-1
  instance_type   = "t2.micro"
  security_groups = [aws_security_group.instances.name]
  user_data       = <<-EOF
              #!/bin/bash
              echo "Hello, World" > index.html
              python3 -m http.server 8080 &
              EOF
}

resource "aws_security_group" "instances" {
  name = "instance-security-group"
}

resource "aws_security_group_rule" "allow_http_inbound" {
  type              = "ingress"
  security_group_id = aws_security_group.instances.id

  from_port   = 8080
  to_port     = 8080
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
}

output "instance_ip_addr" {
  value = aws_instance.instance.public_ip
}


Visual Studio Code
EXPLORER 
OPEN EDITORS 
testing
example
hello-world
main.tf

main.tf
terraform {
  # Assumes s3 bucket and dynamo DB table already set up
  # See /code/03-basics/aws-backend
  backend "s3" {
    bucket         = "devops-directive-tf-state"
    key            = "08-testing/examples/hello-world/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-locking"
    encrypt        = true
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}


module "web_app" {
  source = "../../modules/hello-world"
}

output "instance_ip_addr" {
  value = module.web_app.instance_ip_addr
}

output "url" {
  value = "http://${module.web_app.instance_ip_addr}:8080"
}

Visual Studio Code
EXPLORER 
OPEN EDITORS 
testing
tests
static

static
Static checks
Built in
Format
Enforces style rules for your configurations.

terraform fmt -check # checks if formatter would make chances

terraform fmt # applies those changes
Validate
Checks that configuration are valid.

Terraform init is required to use validate. If not working with a remote backend, terraform init -backend=false can be used.

terraform validate
Plan
Looking at the resulting Terraform plan can help catch bugs.

terraform plan
Custom Validation Rules
Enforce conditions on variables to prevent misuse

variable "short_variable" {
  type = string

  validation {
    condition = length(var.short_variable) < 4
    error_message = "The short_variable value must be less than 4 characters!"
  }
}
External
There are many 3rd party tools which can check Terraform configurations for potential issues and/or suggest best practices:

tflint
checkov
terrascan
terraform-compliance
snyk
Terraform Sentinel

Visual Studio Code
Terminal
cd module/
ls
cd hello-world
terraform fmt
terraform fmt - -check
instance.tf
terraform fmt

Visual Studio Code
EXPLORER 
OPEN EDITORS 
testing
tests
bash
hello_world_test.sh

hello_world_test.sh
#!/bin/bash
set -euo pipefail

# Change directory to example
cd ../../examples/hello-world

# Create the resources
terraform init
terraform apply -auto-approve

# Wait while the instance boots up
# (Could also use a provisioner in the TF config to do this)
sleep 60 

# Query the output, extract the IP and make a request
terraform output -json |\
jq -r '.instance_ip_addr.value' |\
xargs -I {} curl http://{}:8080 -m 10

# If request succeeds, destroy the resources
terraform destroy -auto-approve

Visual Studio Code
Terminal
cd bash/
ls
./hello_world_test.sh
watch “curl https://18.234.207.51:8080”
exit

Visual Studio Code
EXPLORER 
OPEN EDITORS 
testing
tests
terratest
hello_world_test.go

hello_world_test.go
package test

import (
	"crypto/tls"
	"fmt"
	"testing"
	"time"

	"github.com/gruntwork-io/terratest/modules/http-helper"
	"github.com/gruntwork-io/terratest/modules/terraform"
)

func TestTerraformHelloWorldExample(t *testing.T) {
	// retryable errors in terraform testing.
	terraformOptions := terraform.WithDefaultRetryableErrors(t, &terraform.Options{
		TerraformDir: "../../examples/hello-world",
	})

	defer terraform.Destroy(t, terraformOptions)

	terraform.InitAndApply(t, terraformOptions)

	instanceURL := terraform.Output(t, terraformOptions, "url")
	tlsConfig := tls.Config{}
	maxRetries := 30
	timeBetweenRetries := 10 * time.Second

	http_helper.HttpGetWithRetryWithCustomValidation(
		t, instanceURL, &tlsConfig, maxRetries, timeBetweenRetries, validate,
	)

}

func validate(status int, body string) bool {
	fmt.Println(body)
	return status == 200
}

Visual Studio Code
Terminal
go mod download
go test -v - -timeout 10m

Party 9
Developer Workflows

General Workflow

Write/update code
Run changes locally ( for development environment )
Create pull request
Run Test via Continuous Integration
Deploy to staging via CD ( on merge to main )
Deploy to production via CD (on release )

Going Multi-Account With Terraform on AWS
https://www.hashicorp.com/resources/going-multi-account-with-terraform-on-aws 

Additional Tools
Terragrunt
Cloud Nuke
Makefiles

Continuous Integration / Continuous Delivery
Github Actions
CircleCI
GitLab
Atlantis

Potential Gotchas

Name changes when refactoring 
Sensitive data in Terraform state files
Cloud timeouts
Naming conflicts 
Forgetting to destroy test-infra
Uni-directional version upgrades 
Multiple ways to accomplish same configuration
Some Params are immutable
Out of band changes 

Github UI
Visual Studio Code
terraform.yml
name: "Terraform"

on:
  # Uncomment to enable staging deploy from main
  # push:
  #   branches:
  #     - main
  release:
    types: [published]
  pull_request:

jobs:
  terraform:
    name: "Terraform"
    runs-on: ubuntu-latest
    env:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    defaults:
      run:
        working-directory: 07-managing-multiple-environments/file-structure/staging
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v1
        with:
          terraform_version: 1.0.1
          terraform_wrapper: false

      - name: Terraform Format
        id: fmt
        run: terraform fmt -check

      - name: Terraform Init
        id: init
        run: terraform init

      - name: Terraform Plan
        id: plan
        if: github.event_name == 'pull_request'
        # Route 53 zone must already exist for this to succeed!
        run: terraform plan -var db_pass=${{secrets.DB_PASS }} -no-color
        continue-on-error: true

      - uses: actions/github-script@0.9.0
        if: github.event_name == 'pull_request'
        env:
          PLAN: "terraform\n${{ steps.plan.outputs.stdout }}"
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            const output = `#### Terraform Format and Style 🖌\`${{ steps.fmt.outcome }}\`
            #### Terraform Initialization ⚙️\`${{ steps.init.outcome }}\`
            #### Terraform Plan 📖\`${{ steps.plan.outcome }}\`

            <details><summary>Show Plan</summary>

            \`\`\`${process.env.PLAN}\`\`\`

            </details>

            *Pusher: @${{ github.actor }}, Action: \`${{ github.event_name }}\`*`;

              
            github.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: output
            })

      - name: Terraform Plan Status
        if: steps.plan.outcome == 'failure'
        run: exit 1

      - uses: actions/setup-go@v2
        with:
          go-version: '^1.15.5'
          
      - name : Terratest Execution
        if: github.event_name == 'pull_request'
        working-directory: 08-testing/tests/terratest
        run: |
          go test . -v timeout 10m

      - name: Check tag
        id: check-tag
        run: |
          if [[ ${{ github.ref }} =~ ^refs\/tags\/v[0-9]+\.[0-9]+\.[0-9]+$ ]]; then echo ::set-output name=environment::production
          elif [[ ${{ github.ref }} == 'refs/heads/main' ]]; then echo ::set-output name=environment::staging
          else echo ::set-output name=environment::unknown
          fi

      - name: Terraform Apply Global
        if: github.event_name == 'push' || github.event_name == 'release'
        working-directory: 07-managing-multiple-environments/file-structure/global
        run: |
          terraform init
          terraform apply -auto-approve

      - name: Terraform Apply Staging
        if: steps.check-tag.outputs.environment == 'staging' && github.event_name == 'push'
        run: terraform apply -var db_pass=${{secrets.DB_PASS }} -auto-approve

      - name: Terraform Apply Production
        if: steps.check-tag.outputs.environment == 'production' && github.event_name == 'release'
        working-directory: 07-managing-multiple-environments/file-structure/production
        run: |
          terraform init
          terraform apply -var db_pass=${{secrets.DB_PASS }} -auto-approve

Source Control ( update domain )
Commit

Visual Studio Code
Terminal
git push 

Actions Tab
All workflows
update domain

git checkout -b ‘show-testing-on-pr’
git commit --allow-empty -m “show testing on PRs”
git push
git push --set-upstream origin show-testing-on-pr

Actions
Compare & pull request
Create pull request

Actions Tab
All workflows
update domain
v1.0.0
Show testing on PRs

Pull request 
Show testing on PRs

cd 07-managing-multiple-environments
cd file-structure
ls
cd production
terraform init
terraform destroy
