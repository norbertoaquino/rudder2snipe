# rudder2snipe Usage Examples

## Initial Setup

1. **Configure your credentials in the settings.conf file:**

```bash
cp settings-rudder.conf.example settings.conf
```

2. **Edit the settings.conf file with your information:**

```ini
[rudder]
url = https://your-rudder-server.domain.com
api_token = YOUR-RUDDER-API-TOKEN-HERE

[snipe-it]
url = https://your-snipe-instance.domain.com
apikey = YOUR-SNIPE-API-KEY-HERE
manufacturer_id = 2
defaultStatus = 2
computer_model_category_id = 2

[api-mapping]
name = hostname
_snipeit_mac_address_1 = properties ipHostNumber
_snipeit_os_2 = properties osName
_snipeit_os_version_3 = properties osVersion
```

## Basic Commands

### Connection Test
```bash
python3 rudder2snipe --connection_test
```

### Dry Run Mode (Test without changes)
```bash
python3 rudder2snipe --dryrun --verbose
```

### Normal Execution
```bash
python3 rudder2snipe --verbose
```

### Execution with Rate Limiting (recommended for large instances)
```bash
python3 rudder2snipe --verbose --ratelimited
```

### Force Update All Assets
```bash
python3 rudder2snipe --verbose --force
```

## Field Mapping Rudder → Snipe-IT

rudder2snipe automatically maps the following fields from Rudder to Snipe-IT:

| Rudder Field | Snipe-IT Field | Description |
|--------------|----------------|-------------|
| `hostname` | `name` | Asset name |
| `properties.serialNumber` | `serial` | Serial number |
| `properties.machineType` | `model` | Machine type/model |
| `properties.ipHostNumber` | `_snipeit_mac_address_1` | IP/MAC address |
| `properties.osName` | `_snipeit_os_2` | Operating system |
| `properties.osVersion` | `_snipeit_os_version_3` | OS version |
| `properties.manufacturer` | `_snipeit_manufacturer_5` | Manufacturer |
| `properties.memorySize` | `_snipeit_memory_6` | Memory amount |

## Logs and Debugging

### For detailed logs:
```bash
python3 rudder2snipe --debug
```

### For basic information:
```bash
python3 rudder2snipe --verbose
```

## Troubleshooting

### Rudder Authentication Error
- Check if the Rudder API token is correct
- Confirm the token has read permissions for nodes

### SSL Error
```bash
python3 rudder2snipe --do_not_verify_ssl
```

### Rate Limiting
```bash
python3 rudder2snipe --ratelimited
```

## Advanced Configuration Examples

### Custom User Mapping
```ini
[user-mapping]
rudder_api_field = properties username
```

### Custom Asset Tag
```ini
[snipe-it]
asset_tag = properties serialNumber
```

### Custom Field in Snipe-IT
To map a custom field, first get the DB field name in Snipe-IT and add it to the mapping:

```ini
[api-mapping]
_snipeit_custom_field_123 = properties customProperty
```
