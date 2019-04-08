# Function: evaluate_attribute
This function evaluates an attribute from a resource. It can do this for attributes arbitrarily deep inside the resource since it calls itself recursively.

## Declaration and Arguments

### Declaration
`evaluate_attribute = func(r, attribute)`

### Arguments
* **r**: an instance of a resource to validate that should be given in the initial call in the form "r.applied" or "r.attr" depending on whether r is derived from the plan or the state.
* **attribute**: a string representing the attribute to be evaluated. If the attribute is nested, only use "." between its components even for indices of lists and blocks. Indices represented by long IDs should be converted to indices such as 0, 1, 2, and so on.

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
This function returns the value of the attribute for the resource instance after calling itself recursively if needed to process chained attributes of arbitrary length. However, if any item in the chained attribute has length 0 or is immediately before a number and is not a list, the function returns the string "undefined". This could indicate that the attribute was not present in the Terraform code or that it was computed in which case the chained attribute before the number will be a string instead of a list.

## What It Prints
This function does not print anything. If you want to see the value it returns use the print() function after calling it.

## Code
The Sentinel code for this function is in [evaluate_attribute.sentinel](./evaluate_attribute.sentinel).

## Examples
Here is an example of using this function:
```
applied_value = evaluate_attribute(r, attribute)
print("applied value:", applied_value)
```
See the functions [validate_nested_resource_attribute_in_list](./validate_nested_resource_attribute_in_list.md) and [validate_nested_resource_attribute_contains_list](./validate_nested_resource_attribute_contains_list.md) to see this function being used in context.
