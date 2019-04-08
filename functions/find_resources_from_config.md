# Function: find_resources_from_config
This function finds all instances of a specific resource from all modules using the [tfconfig](https://www.terraform.io/docs/enterprise/sentinel/import/tfconfig.html) import.

## Declaration and Arguments

### Declaration
`find_resources_from_config = func(r)`

### Arguments
*r*: the type of resource to find

## Required Imports
This function requires the following import:
```
import "tfconfig"
```
Be sure to include it in any policy that uses this function.

## Custom Functions Used
None

## What It Returns
This function returns a list of of maps, one per module, containing all instances of the specified resource in the config for that module.

## What It Prints
This function does not print anything.

## Code
The Sentinel code for this function is in [find_resources_from_config.sentinel](./find_resources_from_config.sentinel)

## Examples
Here are some examples of using this function:
```
find_resources_from_config("aws_s3_bucket")

find_resources_from_config("azurerm_virtual_machine")

find_resources_from_config("google_compute_instance")

find_resources_from_config("vsphere_nas_datastore")
```
