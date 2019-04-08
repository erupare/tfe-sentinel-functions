# find_resources_from_state
This function finds all instances of a specific resource from all modules using the [tfstate](https://www.terraform.io/docs/enterprise/sentinel/import/tfstate.html) import.

## Declaration and Arguments

### Declaration
`find_resources_from_state = func(r)`

### Arguments
* **r**: the type of resource to find

## Required Imports
This function requires the following import:
```
import "tfstate"
```
Be sure to include it in any policy that uses this function.

## Custom Functions Used
None

## What It Returns
This function returns a list of of maps, one per module, containing all instances of the specified resource in the state for that module.

## What It Prints
This function does not print anything.

## Code
The Sentinel code for this function is in [find_resources_from_state.sentinel](./find_resources_from_state.sentinel).

## Examples
Here are some examples of using this function:
```
find_resources_from_state("aws_s3_bucket")

find_resources_from_state("azurerm_virtual_machine")

find_resources_from_state("google_compute_instance")

find_resources_from_state("vsphere_nas_datastore")
```
