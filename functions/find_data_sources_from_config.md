# Function: find_data_sources_from_config
This function finds all instances of a specific data source from all modules using the [tfconfig](https://www.terraform.io/docs/enterprise/sentinel/import/tfconfig.html) import.

## Declaration and Arguments

### Declaration
`find_data_sources_from_config = func(d)`

### Arguments
*d*: the type of data source to find

## Required Imports
This function requires the following import:
```
import "tfconfig"
```
Be sure to include it in any policy that uses this function.

## Custom Functions Used
None

## What It Returns
This function returns a list of of maps, one per module, containing all instances of the specified data source in the config for that module.

## What It Prints
This function does not print anything.

## Code
The Sentinel code for this function is in [find_data_sources_from_config.sentinel](./find_data_sources_from_config.sentinel).

## Examples
Here are some examples of using this function:
```
find_data_sources_from_config("aws_s3_bucket")

find_data_sources_from_config("azurerm_virtual_machine")

find_data_sources_from_config("google_compute_instance")

find_data_sources_from_config("vsphere_datastore")
```
