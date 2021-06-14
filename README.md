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
* *terraform destroy* - Destroys the resources associated with the original creation. This is the inverse of the terrafrom apply.


# Using Terraform Variables:
* You can include variables in a seperate .tf file. For example, you could define variables.tf and then include the following:

variable "instance_name" {
  description = "Value of the Name tag for the EC2 instance"
  type        = string
  default     = "DavidsExampleAppServerInstance"
}

In main.tf you can then update the tag field as follows:

 tags = {
    Name = var.instance_name
  }
  
  when Terraform executes, it will reach into the variables.tf file to replce the [var.instance_name] with the associated default name from the template "DavidsExampleAppServerInstance"
  
  You can also overide this default form the command line by passing a variable like:
  
  *terraform apply -var "instance_name="YetAnotherName"
  
  **For additional information on setting variable** [Customize Terraform Configuration with variables](https://learn.hashicorp.com/tutorials/terraform/variables?in=terraform/configuration-language)
  
# Terraform Outputs

You can add a output file called outputs.tf with the following configuration for example:

output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.app_server.id
}

output "instance_public_ip" {
  description = "Public IP address of the EC2 instance"
  value       = aws_instance.app_server.public_ip
}

output "instance_private_ip" {
  description = "Private IP address of the EC2 instance"
  value       = aws_instance.app_server.private_ip
}


output "instance_public_dns" {
  description = "Public DNS of the EC2 instance"
  value       = aws_instance.app_server.public_dns
}

When you apply the terraform configuration, it will update this with the instance if and Public IP. To view the output use the folling command:
*terraform output*

Terraform Cloud can be used to manage terraform as well. Things that you can do with terraform cloud:
* Store state about your infrastrcture so you can share/manage with team members
* Store terraform configuration for versioning
* Execute terraform changes, etc... from the cloud or from local machine.

# Terraform Cloud Setup:
The following needs added to the main.tf file to setup the backend for terraform cloud

terraform {
backend "remote" {
  organization = "<ORG_NAME>"
  workspaces {
    name = "Example-Workspace"
  }
}

*terraform login* - Allows you to login to terraform cloud and retrieve a key
*terrafrom init* - required to initialize or reinitalize a terraform directory to use the backend
You must set variables in the terraform work space, be sure to make them sensitive
* AWS_ACCESS_KEY_ID
* AWS_SECRET_ACCESS_KEY

Now apply the configuration using ***terraform apply***

# Creating multiple resources of a similiar type:
 You can use the count or the for_each arguments found [here](https://www.terraform.io/docs/language/meta-arguments/count.html)

//Add count to add 3 instances.. include the ${count.index} command in the name.

resource "aws_instance" "web" {
  count = 3   #Creates three EC2 instances
  
  ami                    = "ami-a0cfeed8"
  instance_type          = "t2.micro"
  user_data              = file("init-script.sh")
  vpc_security_group_ids = [aws_security_group.web-sg.id]

  tags = {
    Name = "${random_pet.name.id} ${count.index}" #${count.index} used as part of the multiple instances being
  }
}

I became stupmped on this part of output where I could't figure out how to use the path
 
 
#I want able to output the dns name using the * to iterate over each instance being created with count, but I couldnt figure out how to append the path.
output "domain-name" {
 value = ["${aws_instance.web.*.public_dns}"]
}

//Couldn't figure out how to make this work with count... Kept getting errors when trying to include the /index.php path

#output "application-url" {
#  value = ["${aws_instance.web.*.public_dns("index.php")}"]
#}
 

# Next Steps:
- [ ] Learn how to manage and version terraform files in Terraform Cloud
- [ ] [Configuration Language](https://learn.hashicorp.com/collections/terraform/configuration-language) - Get more familiar with variables, outputs, dependencies, meta-arguments, and other language features to write more sophisticated Terraform configurations.
  - [X] Define Infrastructure with Terraform Resources
- [ ] [Modules](https://learn.hashicorp.com/tutorials/terraform/module) - Organize and re-use Terraform configuration with modules
- [ ] [Provision](https://learn.hashicorp.com/collections/terraform/provision) - Use Packer or Cloud-init to automatically provision SSH keys and a web server onto a Linux VM created by Terraform in AWS.
- [ ] [Import](https://learn.hashicorp.com/tutorials/terraform/state-import) - Import existing infrastructure into Terraform.
- [ ] To read more about available configuration options, explore the [Terraform documentation](https://www.terraform.io/docs/index.html).
- [ ] Learn more about Terraform Cloud - Although Terraform Cloud can act as a standard remote backend to support Terraform runs on local machines, it works even better as a remote run environment. It supports two main workflows for performing Terraform runs:
  * A VCS-driven workflow, in which it automatically queues plans whenever changes are committed to your configuration's VCS repo.
  * An API-driven workflow, in which a CI pipeline or other automated tool can upload configurations directly.
  For a hands-on introduction to the Terraform Cloud VCS-driven workflow, [follow the Terraform Cloud getting started tutorials](https://learn.hashicorp.com/collections/terraform/cloud-get-started). Terraform Cloud also offers commercial solutions which include team permission management, policy enforcement, agents, and more.

# Terrafrom Resources [resource tutorial](https://learn.hashicorp.com/tutorials/terraform/resource?in=terraform/configuration-language)
* [AWS Provider Documentation](https://learn.hashicorp.com/tutorials/terraform/resource?in=terraform/configuration-language)
