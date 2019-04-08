# Function: evaluate_repeated_attribute
This function evaluates a repeated attribute from a resource. It can do this for attributes arbitrarily deep inside the resource since it calls itself recursively.

## Declaration and Arguments

### Declaration
`evaluate_repeated_attribute = func(r, attribute)`

### Arguments
* **r**: an instance of a resource to validate that should be given in the initial call in the form "r.applied" or "r.attr" depending on whether r is derived from the plan or the state.
* **attribute**: a string representing a nested repeated attribute of the resource. It is represented by a chain of nested attributes separated by "." such as `ingress.*.cidr_blocks.*` in which indices that could be repeated are represented by `*`.

## Required Imports
This function requires the following imports:
```
import "strings"
import "types"
```
Be sure to include them in any policy that uses this function.

## Custom Functions Used
None

## What It Returns
This function returns a list with the values of the repeated attribute for the resource instance after calling itself recursively if needed to process chained attributes of arbitrary length. However, it will sometimes return the empty list, `[]`. This could indicate that the attribute was not present in the Terraform code or that it was computed for all occurences.

## What It Prints
This function does not print anything. If you want to see the values it returns use the print() function after calling it.

## Code
The code for this function is in [evaluate_repeated_attribute.sentinel](./evaluate_repeated_attribute.sentinel).

## Examples
Here is an example of using this function:
```
applied_values = evaluate_repeated_attribute(r, attribute)
print("applied values:", applied_values)
```
See the function [validate_repeated_nested_resource_attribute_in_list](./validate_repeated_nested_resource_attribute_in_list.md) to see this function being used in context.
