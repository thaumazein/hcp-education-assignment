# Getting Started with Terraform

Terraform is the most popular language for defining and provisioning infrastructure as code (IaC).

To install Terraform, visit [Terraform.io](https://www.terraform.io/downloads.html) and download the appropriate binary for your platform, machine, or environment on which you like to run code and do development.

With Terraform installed, you can start creating some infrastructure.

Most people find it easiest to create a new directory on their local machine and then create Terraform configuration code inside it.

```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

Next, create a file for your Terraform configuration code.

```shell
$ touch main.tf
```

Paste the following lines into the file.

```hcl
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
  host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}
resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```

Initialize Terraform with the `terraform init` command, which will install the AWS provider.

```shell
$ terraform init
```

You should check for any errors. If the `terraform init` command ran successfully, provision the resource with the `terraform apply` command.

```shell
$ terraform apply
```

This command will take up to a few minutes to run and will display a message indicating that the resource was created.

You have now created infrastructure using Terraform. Visit localhost in your web browser to verify that the container started.

Finally, destroy the infrastructure using the `terraform destroy` command.

```shell
$ terraform destroy
```

Look for a message at the bottom of the output asking for confirmation. Type `yes` and press `ENTER`. Terraform will destroy the resources it created earlier.
