# Learning about Terraform
- Learning about Terraform with Azure.
- Install terraform in your local machine to work with it and setup an account with azure.
- For setting up account with azure, you can take advantage Azure Student Account or Free tier for this purpose. 

## Terraform Lifecycle 
- Terraform has simple commands to work with - terraform init, terraform plan and terraform apply.
- Terraform code is written in HCL (HashiCorp Configuration Language) language.
- For writing code, we need to know the basic structure of a Terraform configuration file and how to define resources using the HCL language.

## Core files
- main.tf - The main configuration file where you define resources.
          - Example: Create an Azure Resource Group and a Storage Account.
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
  
- variables.tf - Store input variables to make your configuration reusable.

  
- outputs.tf - Extract values from resources for external use.

  
- backend.tf - Configure remote backends to store state files (e.g., Azure Blob Storage).

  

### Terraform State
- .tfstate file tracks the current state of your infrastructure.
- Ensures Terraform knows what exists in your infrastructure to avoid duplications.
- Use a remote backend for production environments.
- Encrypt state files when stored remotely.


