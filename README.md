# Description:

This repository serves as a centralized library of reusable Azure DevOps pipeline templates designed to standardize and streamline Infrastructure as Code (IaC) workflows. It contains modular and scalable YAML templates for common Terraform operations such as initialization, validation, and execution planning.

The primary goal of this repository is to promote reusability, maintainability, and consistency across multiple projects by enabling pipelines to reference pre-defined templates instead of duplicating code. By decoupling pipeline logic from individual projects, teams can ensure uniform deployment practices, reduce redundancy, and accelerate delivery.

These templates can be easily integrated into Azure DevOps pipelines using template referencing, making them ideal for enterprise-level DevOps implementations where multiple environments and teams are involved.

### By treating pipelines as code with modularity, this repository enables:

##### Single source of truth for CI/CD patterns
##### Decoupled evolution of pipeline logic from application code
##### Governance at scale without bottlenecking developers
##### Consistent security gates across all services  

## Problem Statement
```
Challenge	                                                  Impact

Duplicate pipeline code across 50+ repos    	              High maintenance overhead, inconsistent standards
Security policies hard to enforce globally	                Audit failures, compliance risks
Pipeline changes require modifying every service	          Slow iteration, deployment bottlenecks
New team onboarding requires pipeline expertise	            Delayed time-to-first-commit

```
---
## Solution
This library transforms ad‑hoc pipeline definitions into modular, reusable building blocks — enabling teams to compose production-grade pipelines in less than 5 lines of YAML.

## Template Catalog

```
Template	                    Stage	                      Responsibility	                                                       SLA
init-job.yml	                Initialize	                  Environment setup, dependency caching, toolchain installation	          < 30s
validate-job.yml	            Validate	                    Static analysis, linting, security scanning (Trivy/Checkov)	            < 2 min
plan-job.yml	                Plan	                        Terraform/OpenTofu plan generation, drift detection	                    < 3 min
apply-job.yml (optional)	    Apply	                      Immutable deployment, rollback readiness, canary support	              < 5 min
test-job.yml (optional)	      Test	                        Integration tests, policy validation (OPA/Sentinel)	                    < 4 min

````
----
## Design Tenets:
##### Fail fast, fail safe – Validate early, break pipelines with actionable errors
##### Immutable templates – Version pinning prevents accidental breaking changes
##### Observability first – Structured logs, OpenTelemetry tracing, Prometheus metrics
##### Least privilege – Ephemeral credentials, just-in-time access
----
## Deploy the Template Repository
```
# Clone the template library
git clone https://github.com/your-org/pipeline-templates.git
cd pipeline-templates

# Tag your first release
git tag -a v1.0.0 -m "Initial production release"
git push origin v1.0.0
```
## Configure Service Connection (Azure DevOps)
```
Project Settings → Service Connections → New → GitHub
- Connection name: github-pipeline-templates
- Select repository: your-org/pipeline-templates
- Grant access to all pipelines
```
---
## Pipeline-Templates-Library/
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
#### # azure-pipelines.yml
```
# azure-pipelines.yml (in your app repo)
resources:
  repositories:
    - repository: templates
      type: git
      name: your-project/pipeline-templates   # Azure Repos
      # OR for GitHub:
      # type: github
      # name: your-org/pipeline-templates
      # endpoint: github-service-connection

trigger:
- main

stages:
- stage: CI
  displayName: Continuous Integration
  jobs:
  - template: templates/jobs/init-job.yml@templates
    parameters:
      checkoutRepo: true

  - template: templates/jobs/validate-job.yml@templates
    parameters:
      validationScript: 'scripts/validate.sh'

  - template: templates/jobs/plan-job.yml@templates
    parameters:
      planCommand: 'npm run plan'
```
---

## Prerequisites

### Requirement	Description
```
Azure DevOps Project    Access to Azure DevOps organization
Service Connection	    GitHub or Azure Repos connection configured
Terraform Installed	    Terraform CLI available in pipeline agents
Permissions	            Read access to template repository

```



