# rudder2snipe
## Import/Sync Nodes from Rudder to Snipe-IT
```
usage: rudder2snipe [-h] [-v] [--auto_incrementing] [--dryrun] [-d] [--do_not_update_rudder] [--do_not_verify_ssl] [-r] [-f] [--version] [-u | -ui | -uf] [-uns]

options:
  -h, --help            show this help message and exit
  -v, --verbose         Sets the logging level to INFO and gives you a better
                        idea of what the script is doing.
  --auto_incrementing   You can use this if you have auto-incrementing 
                        enabled in your snipe instance to utilize that 
                        instead of adding the Rudder ID for the asset tag.
  --dryrun              This checks your config and tries to contact both 
                        the Rudder and Snipe-it instances, but exits before 
                        updating or syncing any assets.
  -d, --debug           Sets logging to include additional DEBUG messages.
  --do_not_update_rudder Does not update Rudder with the asset tags stored in 
                        Snipe.
  --do_not_verify_ssl   Skips SSL verification for all requests. Helpful when
                        you use self-signed certificate.
  -r, --ratelimited     Puts a half second delay between API calls to adhere 
                        to the standard 120/minute rate limit
  -f, --force           Updates the Snipe asset with information from Rudder 
                        every time, despite what the timestamps indicate.
  --version             Prints the version and exits.
  -u, --users           Checks out the item to the current user in Rudder if 
                        it's not already deployed
  -ui, --users_inverse  Checks out the item to the current user in Rudder if 
                        it's already deployed
  -uf, --users_force    Checks out the item to the user specified in Rudder no 
                        matter what
  -uns, --users_no_search
                        Doesn't search for any users if the specified fields 
                        in Rudder and Snipe don't match. (case insensitive)
```

## Overview:
This tool will sync assets between a Rudder instance and a Snipe-IT instance. The tool searches for assets based on the serial number, not the existing asset tag. If assets exist in Rudder and are not in Snipe-IT, the tool will create an asset and try to match it with an existing Snipe model. This match is based on the node's machine type being entered as the model number in Snipe. If a matching model isn't found, it will create one.

When an asset is first created, it will fill out only the most basic information. When the asset already exists in your Snipe inventory, the tool will sync the information you specify in the settings.conf file and make sure that the asset_tag field in Rudder matches the asset tag in Snipe, where Snipe's info is considered the authority.

> Because it determines whether Rudder or Snipe has the most recently updated record, there is the potential to have blank data in Rudder overwrite good data in Snipe.

Lastly, if the asset_tag field is blank in Rudder when it is being created in Snipe, then the tool will use rudderid-<rudder_node_id> as the asset tag in Snipe. This way, you can easily filter this out and run scripts against it to correct in the future.

## Requirements:

- Python3 (3.8 or higher) is installed on your system with the requests, json, time, and configparser python libs installed.
- Network access to both your Rudder and Snipe-IT environments.
- A Rudder API token with read permissions for nodes.
- Snipe API key for a user that has edit/create permissions for assets and models. Snipe-IT documentation instructions for creating API keys: [https://snipe-it.readme.io/reference#generating-api-tokens](https://snipe-it.readme.io/reference#generating-api-tokens)

## Installation:

### Mac

1. Install Python 3.8 or later
  - Grab the latest PKG installer from the Python website and run it.

2. Add Python 3.8 or later to your PATH
  - Run the `Update Shell Profile.command` script in the `/Applications/Python 3.X` folder to add `python3.X` your PATH

3. Create a virtualenv for rudder2snipe
  - Create the virtualenv: `mkdir ~/.virtualenv`
  - Add `python3.X` to the virtualenv: `python3.X -m venv ~/.virtualenv/rudder2snipe`
  - Activate the virtualenv: `source ~/.virtualenv/rudder2snipe/bin/activate`

4. Install dependencies
  - `pip install -r /path/to/rudder2snipe/requirements.txt`

5. Configure settings.conf (you can start by copying settings-rudder.conf.example to settings.conf)
6. Run `python rudder2snipe` & profit

### Linux

1. Copy the files to your system (recommend installing to /opt/rudder2snipe/* ). Make sure you meet all the system requirements.
2. Edit the settings.conf to match your current environment - you can start by copying settings-rudder.conf.example to settings.conf. The script will look for a valid settings.conf in /opt/rudder2snipe/settings.conf, /etc/rudder2snipe/settings.conf, or in the current folder (in that order): so either copy the file to one of those locations, or be sure that the user running the program is in the same folder as the settings.conf.

## Configuration - settings.conf:

All of the settings that are listed in the [settings-rudder.conf.example](./settings-rudder.conf.example) are required except for the api-mapping section. It's recommended that you install these files to /opt/rudder2snipe/ and run them from there. You will need valid properties from [Rudder's API](https://docs.rudder.io/api/) to associate fields into Snipe.

### Required

Note: do not add `""` or `''` around any values.

**[rudder]**

- `url`: https://*your_rudder_instance*.com:*port*
- `api_token`: Rudder API token with read permissions

**[snipe-it]**

Check out the [settings-rudder.conf.example](./settings-rudder.conf.example) file for the full documentation

- `url`: http://*your_snipe_instance*.com
- `apikey`: API key generated via [these steps](https://snipe-it.readme.io/reference#generating-api-tokens).
- `manufacturer_id`: The manufacturer database field id for the default manufacturer in your Snipe-IT instance. You will probably have to create a Manufacturer in Snipe-IT and note its ID.
- `defaultStatus`: The status database field id to assign to any assets created in Snipe-IT from Rudder. Usually you will want to pick a status like "Ready To Deploy" - look up its ID in Snipe-IT and put the ID here.
- `computer_model_category_id`: The ID of the category you want to assign to Rudder nodes. You will have to create this in Snipe-IT and note the Category ID

### API Mapping

To get the database fields for Snipe-IT Custom Fields, go to Custom Fields, scroll down past Fieldsets to Custom Fields, click the column selection and button and select the unchecked 'DB Field' checkbox. Copy and paste the DB Field name for the Snipe under api-mapping in settings.conf.

To get the database fields for Rudder, refer to Rudder's [API documentation](https://docs.rudder.io/api/).

You need to set the manufacturer_id for devices in the settings.conf file. To get this, go to Manufacturers, click the column selection button and select the `ID` checkbox.

Some example API mappings can be found below:

- Node Name:              `name = hostname`
- IP Address:             `_snipeit_ip_address_1 = properties ipHostNumber`
- OS Name:                `_snipeit_os_2 = properties osName`
- OS Version:             `_snipeit_os_version_3 = properties osVersion`
- Machine Type:           `_snipeit_machine_type_4 = properties machineType`
- Manufacturer:           `_snipeit_manufacturer_5 = properties manufacturer`
- Memory Size:            `_snipeit_memory_6 = properties memorySize`

More information can be found in the ./rudder2snipe file about associations and valid Rudder node properties.

## Testing

It is *always* a good idea to create a test environment to ensure everything works as expected before running anything in production.

Because `rudder2snipe` only ever reads from Rudder and writes to Snipe-IT, testing with your production Rudder instance is OK. However, this can overwrite good data in Snipe. You can spin up a Snipe instance in Docker pretty quickly ([see the Snipe docs](https://snipe-it.readme.io/docs/docker)).

## Contributing

Thanks to all of the people that have already contributed to the original jamf2snipe project! If you have something you'd like to add to rudder2snipe please help by forking this project then creating a pull request. When working on new features, please try to keep existing configs running in the same manner with no changes.

## Credits

This project is based on the excellent [jamf2snipe](https://github.com/grokability/jamf2snipe) project, adapted to work with Rudder instead of Jamf Pro.

### Original Project
- **Project**: jamf2snipe
- **Author**: Brian Monroe and contributors
- **Repository**: https://github.com/grokability/jamf2snipe
- **License**: MIT License

### Adaptation
- **Project**: rudder2snipe  
- **Adaptation**: Integration with Rudder.io instead of Jamf Pro
- **License**: MIT License (maintaining original license terms)

## Acknowledgments

Special thanks to:
- Brian Monroe and all contributors to the original jamf2snipe project
- The Rudder.io team for providing an excellent configuration management platform
- The Snipe-IT team for maintaining a robust asset management system
