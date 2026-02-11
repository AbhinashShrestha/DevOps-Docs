1. Install 
```
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum -y install terraform
```
2. Enable auto completion
```
touch ~/.bashrc
terraform -install-autocomplete
```
3. Configuration
```
main.tf 
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.1"
    }
  }
}
provider "docker" {}
resource "docker_image" "nginx" {
  name         = "nginx"
  keep_locally = false
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = var.nginx_container_name
  ports {
    internal = 80
    external = 8080
  }
}
resource "docker_image" "mysql" {
  name         = "mysql:8.4.2"
}
resource "docker_container" "mysql" {
  image = docker_image.mysql.image_id
  name = var.merosql_container_name
  must_run = true
  publish_all_ports = true
  ports {
  internal = 3306
  external = 3306 
 }
 env = [
   "MYSQL_ROOT_PASSWORD=root",
  ]
}
variables.tf 
variable "nginx_container_name" {
  description = "Value of the name for the Docker container"
  type        = string
  default     = "MeroNginxContainer"
}
variable "merosql_container_name" {
 default = "merosqlcontainer"
}
outputs.tf 
output "nginx_container_id" {
  description = "ID of the Docker container"
  value       = docker_container.nginx.id
}
output "nginx_image_id" {
  description = "ID of the Docker image"
  value       = docker_image.nginx.id
}
output "mysql_container_id" {
  description = "ID of the Docker container"
  value       = docker_container.mysql.id
}
output "mysql_image_id" {
  description = "ID of the Docker image"
  value       = docker_image.mysql.id
}
```


[Docs](https://developer.hashicorp.com/terraform/tutorials/docker-get-started/install-cli)
