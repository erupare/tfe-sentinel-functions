# Reusable Sentinel Functions for Terraform Enterprise
This repository contains some reusable Sentinel functions that can be used in Terraform Enterprise (TFE) Sentinel policies. They do things like:
* Retrieve all instances of a specific resource or data source from the plan, configuration, or state.
* Validate that a specific top-level or nested attribute of a resource contains items from a list of allowed values.
* Validate that a specific top-level or nested attribute of a resource contains all items from a list of mandatory values.
It also includes some policies that use these functions.

## Using These Functions
Currently, these functions have to be pasted into each policy in which you want to call them. In the future, I hope that TFE will support a library of reusable functions to which users could add these and other custom functions. At that point, pasting the custom functions into the policies that use them would no longer be required.

## The Functions
Each function in this repository has two files:
* A file with name `<function>.md` that documents the function and contains examples of how to call it.
* A file with name `<function>.sentinel` that contains the actual code of the function.

Here are the functions in this repository. Each link is to the function's documentation file which links to the file with the code. Additionally, at the bottom of the each function's documentation file, you'll see links to functions or policies that use it.

These functions find data sources from the plan, configuration, and state:
* [find_data_sources_from_config](./functions/find_data_sources_from_config.md): finds all instances of a specific data source from all modules using the tfconfig import.
* [find_data_sources_from_plan](./functions/find_data_sources_from_plan.md): finds all instances of a specific data source from all modules using the tfplan import.
* [find_data_sources_from_state](./functions/find_data_sources_from_state.md): finds all instances of a specific data source from all modules using the tfstate import.

These functions find resources from the plan, configuration, and state:
* [find_resources_from_config](./functions/find_resources_from_config.md): finds all instances of a specific resource from all modules using the tfconfig import.
* [find_resources_from_plan](./functions/find_resources_from_plan.md): finds all instances of a specific resource from all modules using the tfplan import.
* [find_resources_from_state](./functions/find_resources_from_state.md): finds all instances of a specific resource from all modules using the tfstate import.

These functions match a top-level attribute against a list:
* [validate_top_level_resource_attribute_in_list](./functions/validate_top_level_resource_attribute_in_list.md): validates that a specific top-level attribute of all instances of a specific resource in all modules is within a given list of allowed values.
* [validate_top_level_resource_attribute_contains_list](./functions/validate_top_level_resource_attribute_contains_list.md): validates that a specific top-level attribute that is a map or list contains all members of a given list for all instances of a specific resource in all modules.

These functions match a nested attribute against a list:
* [validate_nested_resource_attribute_in_list](./functions/validate_nested_resource_attribute_in_list.md): validates that a specific nested attribute of all instances of a specific resource in all modules is within a given list of allowed values.
* [validate_nested_resource_attribute_contains_list](./functions/validate_nested_resource_attribute_contains_list.md): validates that a specific nested attribute that is a map or list contains all members of a given list for all instances of a specific resource in all modules.

This function matches a repeated nested attribute against a list:
* [validate_repeated_nested_resource_attribute_in_list](./functions/validate_repeated_nested_resource_attribute_in_list.md): validates that a specific repeated attribute of all instances of a specific resource in all modules is in a given list of allowed values.

These functions evaluate attributes:
* [evaluate_attribute](./functions/evaluate_attribute.md): evaluates an attribute from a resource. It can do this for attributes arbitrarily deep inside the resource since it calls itself recursively.
* [evaluate_repeated_attribute](./functions/evaluate_repeated_attribute.md): evaluates a repeated attribute from a resource. It can do this for attributes arbitrarily deep inside the resource since it calls itself recursively.

## Some Comments on the Functions
The following comments about these functions might be useful or interesting.
1. Functions that support nested attributes support them arbitrarily deep within resources by using recursion to parse the attributes as "chained attributes".
1. The [evaluate_attribute](./functions/evaluate_attribute.md) function and functions that call it expect chained attributes like `tags.Name` and  `storage_image_reference.0.publisher`. This function expects any index of a list to only occur once. If a plan log for the resource you are working with shows a longer ID, please use 0 instead. You could also use 1, 2, 3, etc. if calling the function multiple times to process multiple values of the index, but it is probably then better to use a function that calls the evaluate_repeated_attribute function instead of this one.  This function returns the actual value of the evaluated attribute.
1. The [evaluate_repeated_attribute](./functions/evaluate_repeated_attribute.md) function and functions that call it expect chained attributes like `ingress.*.cidr_blocks.*` in which `*` represents repeating indices. This function returns a list of evaluated attributes.
1. I use the `r.applied[attribute1][attribute2][...][attributeN]` syntax instead of the more familiar `r.applied.attribut1.attribute2.(...).attributeN` syntax since the attribute components end up being strings which can be used in the first syntax.

## Policies That Use the Functions
The following polices use many of the above functions. All of the functions starting with "validate" and "evaluate" are covered, but the `find_resources_from_plan` function is the only one of the "find" functions included. Using the other "find" functions is similar to using that one.
* [restrict_aws_instance_types](./policies/restrict_aws_instance_types.sentinel): restricts all instances of the aws_instance resource in the plan to only allow instance_type (size) from the allowed_types list. This uses the [validate_top_level_resource_attribute_in_list](./functions/validate_top_level_resource_attribute_in_list.md) and [find_resources_from_plan](./functions/find_resources_from_plan.md) functions.
* [enforce_mandatory_tags_on_aws_instances](./policies/enforce_mandatory_tags_on_aws_instances.sentinel): restricts all instances of the aws_instance resource in the plan to have all tags in the mandatory_tags list. It uses the [validate_top_level_resource_attribute_contains_list](./functions/validate_top_level_resource_attribute_contains_list.md) and [find_resources_from_plan](./functions/find_resources_from_plan.md) functions.
* [require_private_acl_and_kms_for_s3_buckets](./policies/require_private_acl_and_kms_for_s3_buckets.sentinel): restricts all instances of the aws_s3_bucket resource in the plan to use an acl in the allowed_acls list and a KMS encryption algorithm in the allowed_algorithms list. It uses the [validate_nested_resource_attribute_in_list](./functions/validate_nested_resource_attribute_in_list.md), [find_resources_from_plan](./functions/find_resources_from_plan.md), and [evaluate_attribute](./functions/evaluate_attribute.md) functions.
* [restrict_azure_vm_publishers](./policies/restrict_azure_vm_publishers.sentinel): restricts all instances of the azurerm_virtual_machine resource in the plan to only allow a publisher from the allowed_publishers list. It uses the [validate_nested_resource_attribute_in_list](./functions/validate_nested_resource_attribute_in_list.md), [find_resources_from_plan](./functions/find_resources_from_plan.md), and [evaluate_attribute](./functions/evaluate_attribute.md) functions.
* [restrict_aws_security_group_ingress_cidrs](./policies/restrict_aws_security_group_ingress_cidrs.sentinel): restricts all instances of the aws_security_group resource in the plan to only allow CIDRs in the repeated, nested attribute `ingress.*.cidr_blocks.*` to come from the allowed_cidrs list. Note that `*` represents indices that can be repeated. It uses the [validate_repeated_nested_resource_attribute_in_list](./functions/validate_repeated_nested_resource_attribute_in_list.md), [find_resources_from_plan](./functions/find_resources_from_plan.md), and [evaluate_repeated_attribute](./functions/evaluate_repeated_attribute.md) functions.
* [enforce_mandatory_scopes_on_google_compute_instances](./policies/enforce_mandatory_scopes_on_google_compute_instances.sentinel): restricts all instances of the google_compute_instance resource in the plan to have all service account scopes in the mandatory_scopes list. It uses the [validate_nested_resource_attribute_contains_list](./functions/validate_nested_resource_attribute_contains_list.md), [find_resources_from_plan](./functions/find_resources_from_plan.md), and [evaluate_attribute](./functions/evaluate_attribute.md) functions.

## Feedback
If you use these functions or policies and have any problems with them, please open an issue in this repository to give your feedback. I will try to fix the problem and modify the functions if required. In your issue, please share the Terraform code you used to test the policy and the policy you used the functions in.
