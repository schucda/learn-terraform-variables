# Learn Terraform variables

You can use input variables to customize your Terraform configuration with
values that can be assigned by end users of your configuration. Input variables
allow users to re-use and customize configuration by providing a consistent
interface to change how a given configuration behaves.

Follow along with this [tutorial on HashiCorp
Learn](https://learn.hashicorp.com/tutorials/terraform/variables?in=terraform/configuration-language).

# Terraform Commands:
* *terraform init* - Initializes the directory and installs the proviers defined configuration Terraform downloads the aws provider and installs it in a hidden subdirectory of your current working directory, named .terraform. The terraform init command prints out which version of the provider was installed. Terraform also creates a lock file named .terraform.lock.hcl which specifies the exact provider versions used, so that you can control when you want to update the providers used for your project.
* *terraform fmt* - Formats the terraform files and returns any files that were modified if necessary
* *terraform validate* - Validates that the configuration you have created is correct.
* *terraform apply* - Applies the configuration that you have created.
  * The apply command is used to also update existing resources. For example, if you want to change an AMI, update the main.tf file with the new AMI, then apply. This will destroy the old instance and redeploy with new AMI.
* *terraform show* - Displays the current state of the 
* *terraform state list* - Displays more specific results about specific terraform managed resources.
