# Getting Started with Terraform

Terraform is the most popular language for defining and provisioning infrastructure as code (IaC). It allows you to manage your infrastructure using human-readable, plaintext configuration files. In this tutorial, you will use Terraform to provision a Docker container, which will deploy a running Nginx server on your local machine.

## Prerequisites

- Docker for [Mac](https://docs.docker.com/docker-for-mac/install/), [Windows](https://docs.docker.com/docker-for-windows/install), or [Linux](https://docs.docker.com/engine/install/)


## Install Terraform

To install Terraform, visit [Terraform.io](https://www.terraform.io/downloads.html) and download the appropriate binary for your platform, machine, or environment on which you like to run code and do development.

With Terraform installed, you can start creating some infrastructure.


## Write configuration

Create a new directory on your local machine.

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


## Create infrastructure

Initialize Terraform with the `terraform init` command, which will install the Docker provider.

```shell
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of kreuzwerker/docker...
- Installing kreuzwerker/docker v2.15.0...
- Installed kreuzwerker/docker v2.15.0 (self-signed, key ID BD080C4571C6104C)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

You should check for any errors. If the `terraform init` command ran successfully, provision the resource with the `terraform apply` command.

```shell
$ terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions
are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + entrypoint       = (known after apply)
      + env              = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + init             = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = "json-file"
      + logs             = false
      + must_run         = true
      + name             = "training"
      + network_data     = (known after apply)
      + read_only        = false
      + remove_volumes   = true
      + restart          = "no"
      + rm               = false
      + security_opts    = (known after apply)
      + shm_size         = (known after apply)
      + start            = true
      + stdin_open       = false
      + tty              = false

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id          = (known after apply)
      + latest      = (known after apply)
      + name        = "nginx:latest"
      + output      = (known after apply)
      + repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```

Look for a message at the bottom of the output asking for confirmation. Type `yes` and press `ENTER`. This command may take up to a few minutes to run and will display a message indicating that the resource was created.

```shell
  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 9s [id=sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=9ea8f30d79d54efa43e6fadbac2a6d676610551263b4e3b96ae36bebe65da04f]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

You have now created infrastructure using Terraform. Visit [http://localhost](http://localhost) in your web browser to view the Nginx default page and therefore verify that the container started.


## Destroy infrastructure

Finally, destroy the infrastructure using the `terraform destroy` command.

```shell
$ terraform destroy
docker_image.nginx: Refreshing state... [id=sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85nginx:latest]
docker_container.nginx: Refreshing state... [id=9ea8f30d79d54efa43e6fadbac2a6d676610551263b4e3b96ae36bebe65da04f]

Terraform used the selected providers to generate the following execution plan. Resource actions
are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env               = [] -> null
      - gateway           = "172.17.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "9ea8f30d79d5" -> null
      - id                = "9ea8f30d79d54efa43e6fadbac2a6d676610551263b4e3b96ae36bebe65da04f" -> null
      - image             = "sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85" -> null
      - init              = false -> null
      - ip_address        = "172.17.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "private" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - name              = "training" -> null
      - network_data      = [
          - {
              - gateway                   = "172.17.0.1"
              - global_ipv6_address       = ""
              - global_ipv6_prefix_length = 0
              - ip_address                = "172.17.0.2"
              - ip_prefix_length          = 16
              - ipv6_gateway              = ""
              - network_name              = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - remove_volumes    = true -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - security_opts     = [] -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - stdin_open        = false -> null

      - storage_opts      = {} -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null
      - tty               = false -> null

      - ports {
          - external = 80 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id          = "sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85nginx:latest" -> null
      - latest      = "sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85" -> null
      - name        = "nginx:latest" -> null
      - repo_digest = "nginx@sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value:
```

Type `yes` at the confirmation prompt and press `ENTER`. Terraform will destroy the resources it created earlier.


```shell
  Enter a value: yes

docker_container.nginx: Destroying... [id=9ea8f30d79d54efa43e6fadbac2a6d676610551263b4e3b96ae36bebe65da04f]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85nginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```

## Next steps

Now that you have provisioned a Docker container using Terraform, you are ready to [explore](https://learn.hashicorp.com/terraform) managing all kinds of infrastructure as code.
