# Function: validate_top_level_resource_attribute_contains_list
This function validates that a specific top-level attribute that is a map or list contains all members of a given list for all instances of a specific resource in all modules. It uses the [tfplan](https://www.terraform.io/docs/enterprise/sentinel/import/tfplan.html) import.

If you need to check a nested attribute deeper within a resource, you can use the more complex function [validate_nested_attribute_contains_list](./validate_nested_attribute_contains_list.md).

## Declaration and Arguments

### Declaration
`validate_top_level_resource_attribute_contains_list = func(resource_type, attribute, required_values)`

### Arguments
* **resource_type**: the type of resource to validate
* **attribute**: a top-level attribute of the resource that is a map or list
* **required_values**: a list of required values for the attribute

## Required Imports
This function requires the following import:
```
import "tfplan"
```
Be sure to include it in any policy that uses this function.

## Custom Functions Used
[find_resources_from_plan](./find_resources_from_plan.md)

## What It Returns
This function returns true or false, depending on whether or not any instances of the resource of the specified type do or do not include all members of the given list in the specified attribute.

## What It Prints
This function prints out the name of the first resource for which the specified attribute does not contain all members of the list along with the attribute, the first member of the list that was missing, and the entire list of required values. The output will look like: `resource <resource> has attribute <attribute> that is missing required value <rv> from the list: <required_values>`

If there are no violations, it prints out `No violators of type <resource_type> for attribute <attribute> against the list: <list>`. In this case, <list> will show the actual items of the list that was matched against.

It also prints out a warning if the attribute does not exist or is computed.

## Code
The Sentinel code for this function is in [validate_top_level_resource_attribute_contains_list.sentinel](./validate_top_level_resource_attribute_contains_list.sentinel).

## Examples
Here are some examples of using this function:
```
mandatory_tags = [
  "Name",
  "Owner",
]

validate_top_level_resource_attribute_contains_list("aws_instance", "tags", mandatory_tags)

validate_top_level_resource_attribute_contains_list("azurerm_virtual_machine", "tags", mandatory_tags)

validate_top_level_resource_attribute_contains_list("google_compute_instance", "metadata", mandatory_tags)

mandatory_properties = [
  "guestinfo.hostname",
]
validate_top_level_resource_attribute_contains_list("vsphere_virtual_machine", "vapp.properties", mandatory_tags)
```
See the policy [enforce_mandatory_tags_on_aws_instances](../policies/enforce_mandatory_tags_on_aws_instances.sentinel) to see this function in context.
