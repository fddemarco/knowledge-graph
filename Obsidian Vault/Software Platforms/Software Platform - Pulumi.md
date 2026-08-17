---
base: "[[Reading List.base]]"
Category:
  - IaC
  - SRE
Author: pulumi
Status: Not started
---
[Repo - Pulumi](https://github.com/pulumi/pulumi)
[Homepage - Pulumi](https://www.pulumi.com/docs/get-started/)
[Pulumi vs Terraform](https://www.pulumi.com/docs/iac/comparisons/terraform/)

Pulumi is a modern [infrastructure as code](https://www.pulumi.com/what-is/what-is-infrastructure-as-code/) (IaC) platform that lets you use familiar programming languages and tools to automate, secure and manage everything you run in the cloud.

Pulumi IaC is free, [open source](https://github.com/pulumi/pulumi), and optionally pairs with [Pulumi Cloud](https://www.pulumi.com/docs/iac/guides/basics/pulumi-cloud-vs-oss/) to make managing infrastructure secure, reliable, and hassle-free.

Pulumi and Terraform are both infrastructure as code tools for provisioning and managing cloud resources declaratively. The core difference is how much choice you get: Pulumi runs programs written in general-purpose languages (Python, TypeScript, JavaScript, Go, .NET, and Java), as well as YAML and [HCL](https://www.pulumi.com/docs/iac/languages-sdks/hcl/), while [HashiCorp Terraform](https://developer.hashicorp.com/terraform) uses [HCL](https://developer.hashicorp.com/terraform/language) exclusively. A general-purpose language is the recommended path, because it brings the testing frameworks, package managers, IDE tooling, and AI coding agents that already understand your code. But HCL is a supported language inside Pulumi, so an existing HCL codebase is not a reason to rule Pulumi out.

This page covers what each tool is, a feature-by-feature comparison, real-world results from teams that have adopted Pulumi, the most important differences in detail, and the available paths for adopting Pulumi alongside or instead of Terraform.
