# find_resources_from_plan
This function finds all instances of a specific resource from all modules using the [tfplan](https://www.terraform.io/docs/enterprise/sentinel/import/tfplan.html) import.

## Declaration and Arguments

### Declaration
`find_resources_from_plan = func(r)`

### Arguments
* **r**: the type of resource to find

## Required Imports
This function requires the following import:
```
import "tfplan"
```
Be sure to include it in any policy that uses this function.

## Custom Functions Used
None

## What It Returns
This function returns a list of of maps, one per module, containing all instances of the specified resource in the plan for that module.

## What It Prints
This function does not print anything.

## Code
The Sentinel code for this function is in [find_resources_from_plan.sentinel](./find_resources_from_plan.sentinel).

## Examples
Here are some examples of using this function:
```
find_resources_from_plan("aws_s3_bucket")

find_resources_from_plan("azurerm_virtual_machine")

find_resources_from_plan("google_compute_instance")

find_resources_from_plan("vsphere_nas_datastore")
```
