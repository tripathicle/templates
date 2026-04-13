# Description:

This repository serves as a centralized library of reusable Azure DevOps pipeline templates designed to standardize and streamline Infrastructure as Code (IaC) workflows. It contains modular and scalable YAML templates for common Terraform operations such as initialization, validation, and execution planning.

The primary goal of this repository is to promote reusability, maintainability, and consistency across multiple projects by enabling pipelines to reference pre-defined templates instead of duplicating code. By decoupling pipeline logic from individual projects, teams can ensure uniform deployment practices, reduce redundancy, and accelerate delivery.

These templates can be easily integrated into Azure DevOps pipelines using template referencing, making them ideal for enterprise-level DevOps implementations where multiple environments and teams are involved.

```
Pipeline-Templates-Library/
│
├── templates/
│   ├── terraform-init.yml
│   ├── terraform-validate.yml
│   ├── terraform-plan.yml
│
├── variables/
│   └── common-vars.yml
│
└── README.md

```
---
# Usage
## Step 1: Add Repository Resource in Azure DevOps Pipeline
```
resources:
  repositories:
    - repository: templates
      type: github
      name: <your-username>/Pipeline-Templates-Library
      endpoint: <service-connection-name>
```
---
## Step 2: Call Template in Pipeline

```
jobs:
- template: templates/terraform-init.yml@templates
  parameters:
    workingDir: '$(System.DefaultWorkingDirectory)/infra'
    backendService: 'your-service-connection'

```
---

## Step 3: Reference Templates in Your Pipeline
```
# azure-pipelines.yml
trigger:
- main

stages:
- stage: TerraformCI
  displayName: 'Terraform CI Pipeline'
  jobs:
    - template: templates/jobs/init-job.yml@templates
      parameters:
        checkoutRepo: true

    - template: templates/jobs/validate-job.yml@templates
      parameters:
        validationScript: 'terraform validate'

    - template: templates/jobs/plan-job.yml@templates
      parameters:
        planCommand: 'terraform plan -out=tfplan'
```
---
## Template Examples
```
parameters:
- name: checkoutRepo
  type: boolean
  default: true

jobs:
- job: Init
  displayName: 'Terraform Init'
  steps:
  - checkout: ${{ parameters.checkoutRepo }}
  - script: |
      terraform init
      echo "Terraform initialized successfully"
```
## Prerequisites
### Requirement	Description
```
Azure DevOps Project    Access to Azure DevOps organization
Service Connection	    GitHub or Azure Repos connection configured
Terraform Installed	    Terraform CLI available in pipeline agents
Permissions	            Read access to template repository

```



