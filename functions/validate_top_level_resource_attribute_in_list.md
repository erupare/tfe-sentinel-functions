# validate_top_level_resource_attribute_in_list
This function validates that a specific top-level attribute of all instances of a specific resource in all modules is within a given list of allowed values. It uses the [tfplan](https://www.terraform.io/docs/enterprise/sentinel/import/tfplan.html) import.

If you need to check a nested attribute deeper within a resource, you can use the more complex function [validate_nested_resource_attribute_in_list](./validate_nested_resource_attribute_in_list.md).

## Declaration and Arguments

### Declaration
`validate_top_level_resource_attribute_in_list = func(resource_type, attribute, allowed_values)`

### Arguments
* **resource_type**: the type of resource to validate
* **attribute**: a top-level attribute of the resource.
* **allowed_values**: the list of allowed values for the attribute

## Required Imports
This function requires the following import:
```
import "tfplan"
```
Be sure to include it in any policy that uses this function.

## Custom Functions Used
* [find_resources_from_plan](./find_resources_from_plan.md)

Be sure to past its code into any policy that uses this function.

## What It Returns
This function returns true or false, depending on whether or not any instances of the resource of the specified type have a value of the specified attribute that is not in the given list.

## What It Prints
This function prints out the name of the first resource for which the specified attribute is not in the list along with the attribute, the value of the attribute, and the complete list. The output will look like: `resource <name> has attribute, <attribute> with value <applied_value> that is not in the list: <allowed_values>.`

If the resource is being destroyed, it is skipped and the function prints a message like: `Skipping resource <name> that is being destroyed`.

If there are no violations, it prints out `No violators of type <resource_type> for attribute <attribute> against the list: <list>`. In this case, <list> will show the actual items of the list that was matched against.

It also prints out a warning if the attribute does not exist or is computed.

## Code
The Sentinel code for this function is in [validate_top_level_resource_attribute_in_list.sentinel](./validate_top_level_resource_attribute_in_list.sentinel).

## Examples
Here are some examples of using this function:
```
allowed_types = [
  "t2.small",
  "t2.medium",
]
validate_top_level_resource_attribute_in_list("aws_instance", "instance_type", allowed_types)

allowed_vm_sizes = [
  "Standard_A1",
  "Standard_A2",
]
validate_top_level_resource_attribute_in_list("azurerm_virtual_machine", "vm_size", allowed_vm_sizes)

allowed_machine_types = [
  "n1-standard-2",
  "n1-standard-4",
]
validate_top_level_resource_attribute_in_list("google_compute_instance", "machine_type", allowed_machine_types)

allowed_security_types =[
  "SEC_KRB5",
  "SEC_KRB5I",
]
validate_top_level_resource_attribute_in_list("vsphere_nas_datastore", "security_type", allowed_security_types)
```
See the policy [restrict_aws_instance_types](../policies/restrict_aws_instance_types.sentinel) to see this function used in context.
