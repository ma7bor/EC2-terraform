```markdown
# EC2 Instance Creation with Terraform

This repository contains Terraform configuration files to create an EC2 instance with an attached security group in AWS.

## Prerequisites

Before you begin, ensure you have the following installed on your local machine:

- [Terraform](https://www.terraform.io/downloads.html)
- [AWS CLI](https://aws.amazon.com/cli/)

## Setup

1. **Clone the Repository**

   Clone this repository to your local machine:

   ```sh
   git clone https://github.com/your-username/your-repo-name.git
   cd your-repo-name
   ```

2. **Configure AWS CLI**

   Make sure you have configured AWS CLI with your credentials:

   ```sh
   aws configure
   ```

   This will prompt you to enter your AWS Access Key ID, Secret Access Key, region, and output format.

3. **Set Up Environment Variables**

   Export your AWS credentials as environment variables:

   ```sh
   export AWS_ACCESS_KEY_ID="your_access_key_id"
   export AWS_SECRET_ACCESS_KEY="your_secret_access_key"
   export AWS_REGION="your_aws_region"
   ```

## Terraform Configuration

### `main.tf`

This is the main Terraform configuration file that defines the resources:

```hcl
provider "aws" {
    region     = var.AWS_REGION
    access_key = var.AWS_ACCESS_KEY
    secret_key = var.AWS_SECRET_KEY
}

variable "AWS_ACCESS_KEY" {
    default = "your access key here"
}

variable "AWS_SECRET_KEY" {
    default = "your secret key here"
}

variable "AWS_REGION" {
    default = "your region here"
}

resource "aws_security_group" "instance_security" {
    name = "terraform-test"

    egress {
        from_port       = 0
        to_port         = 0
        protocol        = "-1"
        cidr_blocks     = ["0.0.0.0/0"]
    }

    ingress {
        from_port   = 80
        to_port     = 80
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
    }
}

resource "aws_instance" "my_ec2_instance" {
    ami                  = "ami-005e54dee72cc1d00" # us-west-2
    instance_type        = "t2.micro"
    vpc_security_group_ids = [aws_security_group.instance_security.id]

    user_data = <<-EOF
        #!/bin/bash
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo systemctl start apache2
        sudo systemctl enable apache2
        sudo echo "<h1>Hello devopssec</h1>" > /var/www/html/index.html
    EOF
    
    tags = {
        Name = "terraform test"
    }
}

output "public_ip" {
    value = aws_instance.my_ec2_instance.public_ip
}
```

### Steps to Execute Terraform

1. **Initialize Terraform**

   Run the following command to initialize your Terraform working directory:

   ```sh
   terraform init
   ```

   **Output:**

   ```plaintext
   Initializing the backend...
   Initializing provider plugins...
   - Reusing previous version of hashicorp/aws from the dependency lock file
   - Using previously-installed hashicorp/aws v5.57.0

   Terraform has been successfully initialized!

   You may now begin working with Terraform. Try running "terraform plan" to see
   any changes that are required for your infrastructure. All Terraform commands
   should now work.

   If you ever set or change modules or backend configuration for Terraform,
   rerun this command to reinitialize your working directory. If you forget, other
   commands will detect it and remind you to do so if necessary.
   ```

2. **Validate the Configuration**

   Run the following command to validate your configuration files:

   ```sh
   terraform validate
   ```

   **Output:**

   ```plaintext
   Success! The configuration is valid.
   ```

3. **Apply the Configuration**

   Run the following command to apply your configuration and create the resources:

   ```sh
   terraform apply
   ```

   Terraform will show you an execution plan and ask for confirmation to proceed.

   **Output:**

   ```plaintext
   Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the    
   following symbols:
     + create

   Terraform will perform the following actions:

     # aws_instance.my_ec2_instance will be created
     + resource "aws_instance" "my_ec2_instance" {
         + ami                                  = "ami-005e54dee72cc1d00"
         + instance_type                        = "t2.micro"
         + tags                                 = {
             + "Name" = "terraform test"
           }
         ...
     }

     # aws_security_group.instance_security will be created
     + resource "aws_security_group" "instance_security" {
         + name                   = "terraform-test"
         + ingress                = [
             + {
                 + from_port   = 80
                 + to_port     = 80
                 + protocol    = "tcp"
                 + cidr_blocks = ["0.0.0.0/0"]
               },
           ]
         ...
     }

   Plan: 2 to add, 0 to change, 0 to destroy.

   Changes to Outputs:
     + public_ip = (known after apply)

   Do you want to perform these actions?
     Terraform will perform the actions described above.
     Only 'yes' will be accepted to approve.

     Enter a value: yes
   ```

   Type `yes` to proceed. Upon completion, Terraform will output the public IP of the created EC2 instance:

   ```plaintext
   Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

   Outputs:

   public_ip = "your_ec2_instance_public_ip"
   ```

## Cleaning Up

To destroy the resources created by Terraform, run the following command:

```sh
terraform destroy
```

## Notes

- Ensure your AWS credentials and other sensitive information are never hardcoded in your Terraform files or version-controlled repositories.
- Use environment variables or encrypted storage for sensitive data.

## Contributing

If you find any issues or have suggestions for improvements, feel free to create a pull request or open an issue.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
```

This `README.md` provides step-by-step instructions for setting up, configuring, and executing the Terraform script, including outputs and commands. Make sure to replace placeholders like `"your access key here"`, `"your secret key here"`, and `"your region here"` with actual values or instructions for users to replace them.