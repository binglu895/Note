Terraform
    #1.Terraform Data Sources
        Data sources in Terraform are used to get information about resources external to Terraform, 
        and use them to set up your Terraform resources. For example, a list of IP addresses a cloud provider exposes.

        Example:
            variable "vpc_id" {}

            data "aws_vpc" "selected" {
              id = var.vpc_id
            }

            resource "aws_subnet" "example" {
              vpc_id            = data.aws_vpc.selected.id
              availability_zone = "us-west-2a"
              cidr_block        = cidrsubnet(data.aws_vpc.selected.cidr_block, 4, 1)
            }
            
            In this scenario, we may have created our aws_vpc manually, 
            because it also contains other manually created resources, and now we want to start adding Terraform managed resources to it.

            In order to do this, we use the aws_vpc data source to find the manually created aws_vpc, 
            and then use its properties to configure our aws_subnet.
        
        Example:
            data "github_repository_pull_requests" "pull_requests" {
              base_repository = "example-repository"
              base_ref        = "main"
              state           = "open"
            }

            module “preview-environment” {
              for_each        = data.github_repository_pull_requests.pull_requests.results
              name            = each.value.title
              commit_sha      = each.value.head_sha
              // ...
            }
            
            use the GitHub provider Pull Requests data source to list all Pull Requests, 
            and then provision an on-demand preview environment for each Pull Request
            
