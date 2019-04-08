# Function: validate_repeated_nested_resource_attribute_in_list
This function validates that a specific repeated attribute of all instances of a specific resource in all modules is in a given list of allowed values. The attribute can be nested arbitrarily deep within the resource structure if it is provided as a chain of attributes separated by "." such as `ingress.*.cidr_blocks.*` in which repeated indices are represented by `*`.

It uses the [tfplan](https://www.terraform.io/docs/enterprise/sentinel/import/tfplan.html) import.

## Declaration and Arguments

### Declaration
`validate_repeated_nested_resource_attribute_in_list = func(resource_type, attribute, allowed_values)`

### Arguments
* **resource_type**: the type of resource to validate
* **attribute**: a string representing a nested repeated attribute of the resource. It is represented by a chain of nested attributes separated by "." such as `ingress.*.cidr_blocks.*` in which repeated indices are represented by `*`.
* **allowed_values**: the list of allowed values for the attribute

## Required Imports
This function requires the following imports:
```
import "tfplan"
import "strings"
import "types"
```
Be sure to include them in any policy that uses this function. The "strings" and "types" imports are needed by the evaluate_repeated_resource_attribute function that this function calls.

## Custom Functions Used
[find_resources_from_plan](./find_resources_from_plan.md)
[evaluate_repeated_resource_attribute](./evaluate_repeated_resource_attribute.md)
Be sure to past their code into your policy.

## What It Returns
This function returns true or false, depending on whether or not any instances of the resource of the specified type have a value of the specified attribute that is not in the given list. It also returns false when the no occurences of the attribute are found or are all computed.

## What It Prints
This function prints out the name of the first resource for which an occurence of the specified attribute is not in the list along with the attribute, the value of the attribute, and the complete list. The output will look like: `resource <name> has attribute, <attribute> with value <applied_value> that is not in the list: <allowed_values>.`

If the attribute is missing or computed, it prints a message like: `resource <name> has attribute <attribute> that is missing or computed`.

If there are no violations, it prints out `No violators of type <resource_type> for attribute <attribute> against the list: <list>`. In this case, <list> will show the actual items of the list that was matched against.

## Code
The Sentinel code for this function is in [validate_repeated_nested_resource_attribute_in_list.sentinel](./validate_repeated_nested_resource_attribute_in_list.sentinel).

## Examples
Here are some examples of using this function. When a possibly repeating component of an attribute is indexed with a number, you should replace it with `*`.
```
# Allowed CIDRs
allowed_cidrs = [
  "172.16.10.0/24",
  "172.16.80.0/24",
]
validate_repeated_nested_resource_attribute_in_list("aws_security_group", "ingress.*.cidr_blocks.*", allowed_cidrs)
```
See the policy [restrict_aws_security_group_ingress_cidrs](../policies/restrict_aws_security_group_ingress_cidrs.sentinel) to see this function in context.
