# Azure Backup - VM Monitor

It includes:
  * A Data Collection Rule to gather metrics and logs from Virtual Machines ([documentation](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/data-collection-rule-overview))

## Version compatibility

| Module version    | Terraform version | AzureRM version |
|-------------------|-------------------|-----------------|
| >= 3.x.x          | 0.12.x            | >= 2.0          |
| >= 2.x.x, < 3.x.x | 0.12.x            | <  2.0          |
| <  2.x.x          | 0.11.x            | <  2.0          |

## Usage

This module is optimized to work with the [Claranet terraform-wrapper](https://github.com/claranet/terraform-wrapper) tool
which set some terraform variables in the environment needed by this module.
More details about variables set by the `terraform-wrapper` available in the [documentation](https://github.com/claranet/terraform-wrapper#environment).

```hcl
module "azure_region" {
  source  = "claranet/regions/azurerm"
  version = "x.x.x"

  azure_region = var.azure_region
}

module "rg" {
  source  = "claranet/rg/azurerm"
  version = "x.x.x"

  location    = module.azure_region.location
  client_name = var.client_name
  environment = var.environment
  stack       = var.stack
}

module "logs" {
  source  = "claranet/run-common/azurerm//modules/logs"
  version = "x.x.x"

  client_name    = var.client_name
  location       = module.azure_region.location
  location_short = module.azure_region.location_short
  environment    = var.environment
  stack          = var.stack

  resource_group_name = module.rg.resource_group_name
}

module "vm_monitoring" {
  source  = "claranet/run-iaas/azurerm//modules/vm-monitoring"
  version = "x.x.x"

  location       = module.azure_region.location
  location_short = module.azure_region.location_short
  client_name    = var.client_name
  environment    = var.environment
  stack          = var.stack

  resource_group_name = module.rg.resource_group_name

  log_analytics_workspace_id = module.logs.log_analytics_workspace_id

  extra_tags = {
    foo = "bar"
  }
}
```

<!-- BEGIN_TF_DOCS -->
## Providers

| Name | Version |
|------|---------|
| azurecaf | ~> 1.1 |
| azurerm | ~> 3.15 |

## Modules

No modules.

## Resources

| Name | Type |
|------|------|
| [azurecaf_name.dcr](https://registry.terraform.io/providers/aztfmod/azurecaf/latest/docs/resources/name) | resource |
| [azurerm_monitor_data_collection_rule.dcr](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/monitor_data_collection_rule) | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| client\_name | Client name | `string` | n/a | yes |
| custom\_name | Custom name, generated if not set | `string` | `""` | no |
| default\_tags\_enabled | Option to enable or disable default tags. | `bool` | `true` | no |
| environment | Environment name | `string` | n/a | yes |
| extra\_tags | Extra tags to add | `map(string)` | `{}` | no |
| location | Azure location. | `string` | n/a | yes |
| location\_short | Short string for Azure location. | `string` | n/a | yes |
| log\_analytics\_workspace\_id | Log Analytics Workspace ID where the metrics are sent | `string` | n/a | yes |
| name\_prefix | Optional prefix for the generated name | `string` | `""` | no |
| name\_suffix | Optional suffix for the generated name | `string` | `""` | no |
| resource\_group\_name | Resource Group the resources will belong to | `string` | n/a | yes |
| stack | Stack name | `string` | n/a | yes |
| syslog\_facilities\_names | List of syslog to retrieve in Data Collection Rule | `list(string)` | <pre>[<br>  "auth",<br>  "authpriv",<br>  "cron",<br>  "daemon",<br>  "mark",<br>  "kern",<br>  "local0",<br>  "local1",<br>  "local2",<br>  "local3",<br>  "local4",<br>  "local5",<br>  "local6",<br>  "local7",<br>  "lpr",<br>  "mail",<br>  "news",<br>  "syslog",<br>  "user",<br>  "uucp"<br>]</pre> | no |
| syslog\_levels | List of syslog levels to retrieve in Data Collection Rule | `list(string)` | <pre>[<br>  "Error",<br>  "Critical",<br>  "Alert",<br>  "Emergency"<br>]</pre> | no |
| use\_caf\_naming | Use the Azure CAF naming provider to generate default resource name. | `bool` | `true` | no |

## Outputs

| Name | Description |
|------|-------------|
| data\_collection\_rule | Azure Monitor Data Collection Rule object |
| data\_collection\_rule\_id | ID of the Azure Monitor Data Collection Rule |
| data\_collection\_rule\_name | Name of the Azure Monitor Data Collection Rule |
<!-- END_TF_DOCS -->
