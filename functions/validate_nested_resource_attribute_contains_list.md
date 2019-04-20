# validate_nested_resource_attribute_contains_list
This function validates that a specific attribute that is a map or list contains all members of a given list for all instances of a specific resource in all modules. The attribute can be nested arbitrarily deep within the resource structure if it is provided as a chain of attributes separated by "." such as "tags.Name" or "storage_image_reference.0.publisher".

It uses the [tfplan](https://www.terraform.io/docs/enterprise/sentinel/import/tfplan.html) import.

If you only need to check a top-level attribute of a resource, you can use the simpler function [validate_top_level_resource_attribute_contains_list](./validate_top_level_resource_attribute_contains_list.md). But this function works for top-level attributes too.

## Declaration and Arguments

### Declaration
`validate_nested_resource_attribute_contains_list = func(resource_type, attribute, required_values)`

### Arguments
* **resource_type**: the type of resource to validate
* **attribute**: a string representing a nested attribute of the resource. It is represented with a chain of nested attributes separated by ".". Examples are "tags.Name" and "storage_image_reference.0.publisher".
* **required_values**: a list of required values for the attribute

## Required Imports
This function requires the following imports:
```
import "tfplan"
import "strings"
import "types"
```
Be sure to include them in any policy that uses this function. The "strings" and "types" imports are needed by the evaluate_attribute function that this function calls.

## Custom Functions Used
* [find_resources_from_plan](./find_resources_from_plan.md)
* [evaluate_attribute](./evaluate_attribute.md)

Be sure to past their code into any policy that uses this function.

## What It Returns
This function returns true or false, depending on whether or not any instances of the resource of the specified type do or do not include all members of the given list in the specified attribute. It also returns false when the attribute is missing or computed.

## What It Prints
This function prints out the name of the first resource for which the specified attribute does not contain all members of the list along with the attribute, the first member of the list that was missing, and the entire list of required values. The output will look like: `resource <resource> has attribute <attribute> that is missing required value <rv> from the list: <required_values>`

If the resource is being destroyed, it is skipped and the function prints a message like: `Skipping resource <name> that is being destroyed`.

If the attribute is computed, it prints a message like: `resource <name> has a computed attribute: <attribute>`.

If the attribute is missing, it prints a message like: `resource <name> has a missing attribute: <attribute>`.

If there are no violations, it prints out `No violators of type <resource_type> for attribute <attribute> against the list: <list>`. In this case, <list> will show the actual items of the list that was matched against.

## Code
The Sentinel code for this function is in [validate_nested_resource_attribute_contains_list.sentinel](./validate_nested_resource_attribute_contains_list.sentinel).

## Examples
Here are some examples of using this function:
```
mandatory_tags = [
  "Name",
  "Owner",
]

validate_nested_resource_attribute_contains_list("aws_instance", "tags", mandatory_tags)

validate_nested_resource_attribute_contains_list("azurerm_virtual_machine", "tags", mandatory_tags)

validate_nested_resource_attribute_contains_list("google_compute_instance", "metadata", mandatory_tags)

mandatory_properties = [
  "guestinfo.hostname",
]
validate_nested_resource_attribute_contains_list("vsphere_virtual_machine", "vapp.properties", mandatory_tags)
```
See the policy [enforce_mandatory_scopes_on_google_compute_instances](../policies/enforce_mandatory_scopes_on_google_compute_instances.sentinel) to see this function in context.
