# Learning about Terraform
- Learning about Terraform with Azure.
- Install terraform in your local machine to work with it and setup an account with azure.
- For setting up account with azure, you can take advantage Azure Student Account or Free tier for this purpose. 

## Terraform Lifecycle 
- Terraform has simple commands to work with - terraform init, terraform plan and terraform apply.
- Terraform code is written in HCL (HashiCorp Configuration Language) language.
- For writing code, we need to know the basic structure of a Terraform configuration file and how to define resources using the HCL language.

## Core files

### *main.tf*:
- The main configuration file where you define resources.
- E.g.: Create an Azure Resource Group and a Storage Account.
  
  ```hcl
  provider "azurerm" {
  features {}
  }

  resource "azurerm_resource_group" "example" {
    name     = "example-rg"
    location = "East US"
  }

  resource "azurerm_storage_account" "example" {
    name                     = "examplestorageacct"
    resource_group_name      = azurerm_resource_group.example.name
    location                 = azurerm_resource_group.example.location
    account_tier             = "Standard"
    account_replication_type = "LRS"
  }
  
### *variables.tf*:
- Store input variables to make your configuration reusable.
  
  ```hcl
     variable "location" {
     default = "East US"
     description = "The Azure region to deploy resources"
  }

     variable "resource_group_name" {
     type        = string
     description = "The name of the resource group"
  }

### *outputs.tf*:
- Extract values from resources for external use.

  ```hcl
  output "storage_account_name" {
  value = azurerm_storage_account.example.name
  }
  
### *backend.tf*:
- Configure remote backends to store state files.
- E.g. Azure Blob Storage.

  ```hcl
     terraform {
     backend "azurerm" {
     resource_group_name  = "terraform-backend-rg"
     storage_account_name = "terraformstate123"
     container_name       = "tfstate"
     key                  = "terraform.tfstate"
     }
  }

## Terraform State
- .tfstate file tracks the current state of your infrastructure.
- Ensures Terraform knows what exists in your infrastructure to avoid duplications.
- Use a remote backend for production environments.
- Encrypt state files when stored remotely.

## Terraform Modules: Reusable Components
- In Terraform, modules are a way to organize and reuse infrastructure code.
- A module is simply a container for multiple resources that are used together.
- By using modules, you can group related resources into a logical unit and reuse that unit in different configurations.
- Modules provide a way to abstract complex configurations and make them reusable in different environments or projects.
- It promotes DRY (Don't Repeat Yourself) principles.

**How to Write a Module in Terraform?**  
*module consists of*:  
- Input Variables (variables.tf): Allow customization of the module.
- Resources (main.tf): Define the resources that the module creates.
- Outputs (outputs.tf): Expose the results of the module’s resources to be used by other parts of the Terraform configuration.
