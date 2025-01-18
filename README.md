# Learning about Terraform
- Learning about Terraform with Azure.
- Install terraform in your local machine to work with it and setup an account with azure.
- For setting up account with azure, you can take advantage Azure Student Account or Free tier for this purpose.

*What is Terraform?*  
- Terraform is a tool that helps us create, manage and organize infrastructure (like servers, databases and networks) using code.
- Instead of manually setting up everything, we write simple scripts (called "Terraform configuration files") to tell Terraform what we want, and it does the setup for us.

## Terraform Lifecycle 
- Terraform has simple commands to work with - terraform init, terraform plan and terraform apply.
- Terraform code is written in HCL (HashiCorp Configuration Language) language.
- For writing code, we need to know the basic structure of a Terraform configuration file and how to define resources using the HCL language.

![Terraform Architecture](https://github.com/user-attachments/assets/e7cee54c-377d-4c15-8e4f-a70f6397b79d)    

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

![Deploying resources on Azure using Terraform](https://github.com/user-attachments/assets/cfb16243-9a6e-40cd-a0b8-6c0aae76947f)

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
- When we run terraform commands from within the minimal-module directory, the contents of that directory are considered the root module.

![root module](https://github.com/user-attachments/assets/fd713b37-ff26-4787-8f1f-2f6272f4324f)

**Module in simple words is**
- A Terraform module is a set of Terraform configuration files in a single directory.
- Even a simple configuration consisting of a single directory with one or more .tf files is a module. When you run Terraform commands directly from such a directory, it is considered the root module.

![terraform modules](https://github.com/user-attachments/assets/ead71719-ecd1-4b06-88b6-cd357d0d471b)  

## State Management 
- When managing infrastructure with Terraform, the state file is a crucial component. It tracks the current state of your resources, ensuring Terraform knows what already exists and what needs to be created, updated, or deleted.

*What is Terraform State?*
- Purpose: The state file keeps a record of all the resources Terraform manages. It acts as a source of truth for your infrastructure.
- File Format: Terraform state is typically stored in a file called terraform.tfstate.
- Location: By default, the state file is stored locally in your project directory, but it can be configured to use remote backends.

**Remote State Management**
- Storing the state file remotely has several benefits, including collaboration, security, and reliability.  
  e.g. Azure Blob Storage: backend "azurerm" { ... }
  ```hcl
  terraform {
    backend "azurerm" {
      resource_group_name   = "myResourceGroup"
      storage_account_name  = "mystorageaccount"
      container_name        = "tfstate"
      key                   = "terraform.tfstate"
    }
  }

**State Locking**
- State locking prevents multiple users from making concurrent changes to the state file, avoiding potential conflicts and ensuring data integrity.
- e.g. Using Azure Blob Storage for State Locking  
       Azure Blob Storage supports state locking natively. No additional configuration is required beyond setting up the backend.

**State Encryption**
- Encrypting state files is essential to protect sensitive data. Remote backends like AWS S3 and Azure Blob Storage provide encryption options.
e.g. Enabling Encryption in Azure Blob Storage
     Azure Blob Storage encrypts data at rest and in transit by default. Ensure you configure your backend to use these security features.

### Advanced State Management

**State Workspaces**
- Workspaces allow you to manage multiple state files for different environments (e.g., dev, test, prod) within the same Terraform configuration.
e.g. Creating and Switching Workspaces

  ```bash
    # Create a new workspace
    terraform workspace new dev
    
    # Switch to an existing workspace
    terraform workspace select dev
  
**State Manipulation**
- Sometimes, we might need to modify the state file directly. Terraform provides several commands to help with this:
  - terraform state list: List all resources in the state file.
  - terraform state show <resource>: Show detailed information about a specific resource.
  - terraform state mv <source> <destination>: Move a resource from one state to another.
  - terraform state rm <resource>: Remove a resource from the state file.

*Good Practices for State Management:*

- Use Remote Backends: Always use remote backends for production environments to enhance collaboration and security.
- Enable State Locking: Prevent concurrent changes to the state file to avoid conflicts.
- Encrypt State Files: Protect sensitive data by encrypting state files at rest and in transit.
- Use Workspaces: Separate state files for different environments using workspaces.
- Backup State Files: Regularly backup your state files to prevent data loss.
