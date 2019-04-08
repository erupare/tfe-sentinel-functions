# validate_nested_resource_attribute_in_list
This function validates that a specific attribute of all instances of a specific resource in all modules is within a given list of allowed values. The attribute can be nested arbitrarily deep within the resource structure if it is provided as a chain of attributes separated by "." such as "tags.Name" or "storage_image_reference.0.publisher".

It uses the [tfplan](https://www.terraform.io/docs/enterprise/sentinel/import/tfplan.html) import.

If you only need to check a top-level attribute of a resource, you can use the simpler function [validate_top_level_resource_attribute_in_list](./validate_top_level_resource_attribute_in_list.md). But this function works for top-level attributes too.

## Declaration and Arguments

### Declaration
`validate_nested_resource_attribute_in_list = func(resource_type, attribute, allowed_values)`

### Arguments
* **resource_type**: the type of resource to validate
* **attribute**: a string representing a nested attribute of the resource. It is represented with a chain of nested attributes separated by ".". Examples are "tags.Name" and "storage_image_reference.0.publisher". If the plan shows a long, numeric ID for an index, replace it with 0.
* **allowed_values**: the list of allowed values for the attribute

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
This function returns true or false, depending on whether or not any instances of the resource of the specified type have a value of the specified attribute that is not in the given list. It also returns false when the attribute is missing or computed.

## What It Prints
This function prints out the name of the first resource for which the specified attribute is not in the list along with the attribute, the value of the attribute, and the complete list. The output will look like: `resource <name> has attribute, <attribute> with value <applied_value> that is not in the list: <allowed_values>.`

If the attribute is missing or computed, it prints a message like: `resource <name> has attribute <attribute> that is missing or computed`.

If there are no violations, it prints out `No violators of type <resource_type> for attribute <attribute> against the list: <list>`. In this case, <list> will show the actual items of the list that was matched against.

## Code
The Sentinel code for this function is in [validate_nested_resource_attribute_in_list.sentinel](./validate_nested_resource_attribute_in_list.sentinel).

## Examples
Here are some examples of using this function. Note that when an attribute is in a block indexed with a number different from 0, you should replace it with 0.
```
allowed_types = [
  "t2.small",
  "t2.medium",
]
validate_nested_resource_attribute_in_list("aws_instance", "instance_type", allowed_types)

allowed_publishers = [
  "MicrosoftWindowsServer",
  "RedHat",
]
validate_nested_resource_attribute_in_list("azurerm_virtual_machine", "storage_image_reference.0.publisher", allowed_publishers)

allowed_machine_types = [
  "n1-standard-2",
  "n1-standard-4",
]
validate_nested_resource_attribute_in_list("google_compute_instance", "machine_type", allowed_machine_types)

allowed_security_types =[
  "SEC_KRB5",
  "SEC_KRB5I",
]
validate_nested_resource_attribute_in_list("vsphere_nas_datastore", "security_type", allowed_security_types)

allowed_algorithms = [
  "aws:kms",
]
validate_nested_resource_attribute_in_list("aws_s3_bucket", "server_side_encryption_configuration.0.rule.0.apply_server_side_encryption_by_default.0.sse_algorithm", allowed_algorithms)
```
See the policies [require_private_acl_and_kms_for_s3_buckets](../policies/require_private_acl_and_kms_for_s3_buckets.sentinel) and [restrict_azure_vm_publishers](../policies/restrict_azure_vm_publishers.sentinel) to see this function in context.
