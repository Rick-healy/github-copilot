---
description: 'Bicep infrastructure-as-code conventions for Azure'
applyTo: '**/*.bicep'
---

# Bicep

## General

- **Prefer Azure Verified Modules (AVM)** for common resources. Check [aka.ms/AVM](https://aka.ms/AVM) before writing a resource block from scratch.
- Use the latest stable API version for each resource type. Do not use preview API versions in production templates without explicit justification.
- One responsibility per module. A module that provisions "everything for a service" should compose smaller modules, not inline every resource.

## File layout

- **`main.bicep`** — top-level entry point per environment/deployment scope.
- **`modules/<name>.bicep`** — reusable modules.
- **`main.bicepparam`** — typed parameter files (one per environment).
- Keep infrastructure code in a top-level `infra/` folder unless the project already uses a different location.

## Style

- Two-space indentation.
- Resource symbolic names use `camelCase` (e.g. `storageAccount`), not the resource's actual Azure name.
- Descriptions on every `param` — they appear in the deployment UI and IntelliSense:
  ```bicep
  @description('The region for all resources.')
  param location string = resourceGroup().location
  ```
- Use `@allowed`, `@minLength`, `@maxLength`, `@secure` decorators where relevant.
- Prefer `output` over hardcoded IDs — let the caller wire modules together.

## Parameters

- Every module accepts `location` (default `resourceGroup().location`) and a `tags` object.
- Sensitive parameters (secrets, keys) use `@secure()` and are sourced from Key Vault via `getSecret()` — never inlined.
- Environment-specific values live in `.bicepparam` files, not defaults.

## Naming

- Prefer a **naming convention module** or `uniqueString(resourceGroup().id, …)` for globally-unique names. Don't hardcode.
- Follow the [Azure resource abbreviation guide](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations) for resource prefixes (`st` for Storage Account, `kv` for Key Vault, etc.).

## Identity & secrets

- **Managed identities over connection strings.** Assign RBAC roles (`Microsoft.Authorization/roleAssignments`) — do not embed keys in app settings.
- Reference Key Vault secrets via `existing` + `getSecret()`, not hardcoded values.

## Deployments

- Prefer subscription-scope or resource-group-scope deployments over management-group unless organisationally needed.
- Use `deploymentScripts` sparingly — they run in Azure and can be slow / expensive.
- Idempotency: templates must be safely redeployable. Test with a second deployment before merging.

## Linting & validation

- Run `bicep build main.bicep` (compiles + lints) before submitting.
- Use the Bicep VS Code extension. Fix all warnings, not just errors.
- If AVM modules are used, prefer `br/public:avm/…` refs pinned to a version.
