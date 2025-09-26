# rudder2snipe Wiki - Complete Documentation

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [API Mapping](#api-mapping)
- [User Management](#user-management)
- [Advanced Configuration](#advanced-configuration)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)
- [Examples](#examples)
- [Contributing](#contributing)
- [FAQ](#faq)
- [Credits](#credits)

---

## Overview

**rudder2snipe** is a Python-based tool that synchronizes asset inventory between [Rudder.io](https://rudder.io) (configuration management platform) and [Snipe-IT](https://snipeitapp.com) (asset management system). It automatically creates, updates, and manages hardware assets in Snipe-IT based on node information from Rudder.

### Key Benefits

- **Automated Sync**: Eliminates manual data entry between systems
- **Bidirectional Updates**: Keeps both systems in sync
- **Flexible Mapping**: Customizable field mapping between Rudder and Snipe-IT
- **Asset Lifecycle**: Handles creation, updates, and user assignments
- **Error Recovery**: Built-in retry mechanisms and error handling

---

## Features

### Core Functionality
- ✅ **Asset Discovery**: Automatically finds Rudder nodes and creates corresponding Snipe-IT assets
- ✅ **Model Management**: Creates hardware models in Snipe-IT based on Rudder machine types
- ✅ **Field Mapping**: Maps Rudder node properties to Snipe-IT asset fields
- ✅ **User Assignment**: Associates assets with users based on configurable mapping
- ✅ **Asset Tag Sync**: Synchronizes asset tags between systems (Snipe-IT as authority)
- ✅ **Timestamp Checking**: Only updates when source data is newer than destination
- ✅ **Dry Run Mode**: Test configuration without making changes
- ✅ **Connection Testing**: Verify API connectivity before operations

### Advanced Features
- ✅ **Rate Limiting**: Respects API rate limits (120 requests/minute)
- ✅ **SSL Configuration**: Supports custom SSL verification settings
- ✅ **Force Updates**: Override timestamp checking when needed
- ✅ **Auto-incrementing**: Support for auto-incrementing asset tags
- ✅ **Detailed Logging**: Comprehensive logging with multiple verbosity levels
- ✅ **Error Handling**: Graceful handling of API errors and network issues

---

## Requirements

### System Requirements
- **Python**: 3.7 or higher
- **Operating System**: Linux, macOS, or Windows
- **Network**: Access to both Rudder and Snipe-IT instances

### Python Dependencies
```bash
pip install requests configparser argparse
```
Or use the provided requirements file:
```bash
pip install -r requirements.txt
```

### Access Requirements
- **Rudder API Token**: Read permissions for nodes endpoint
- **Snipe-IT API Key**: Create/edit permissions for assets, models, and users
- **Network Connectivity**: HTTPS access to both API endpoints

---

## Installation

### Method 1: Direct Download
```bash
# Download the latest release
wget https://github.com/norbertoaquino/rudder2snipe/archive/main.zip
unzip main.zip
cd rudder2snipe-main
```

### Method 2: Git Clone
```bash
git clone https://github.com/norbertoaquino/rudder2snipe.git
cd rudder2snipe
```

### Method 3: Production Installation (Linux)
```bash
# Install to /opt (recommended for production)
sudo mkdir -p /opt/rudder2snipe
sudo cp -r * /opt/rudder2snipe/
sudo chown -R rudder:rudder /opt/rudder2snipe
```

### Virtual Environment Setup (Recommended)
```bash
# Create virtual environment
python3 -m venv ~/.virtualenv/rudder2snipe
source ~/.virtualenv/rudder2snipe/bin/activate

# Install dependencies
pip install -r requirements.txt
```

---

## Configuration

### Quick Start Configuration

1. **Copy Example Configuration**:
```bash
cp settings-rudder.conf.example settings.conf
```

2. **Edit Configuration File**:
```bash
vim settings.conf  # or your preferred editor
```

### Configuration File Locations

rudder2snipe searches for `settings.conf` in the following order:
1. `/opt/rudder2snipe/settings.conf`
2. `/etc/rudder2snipe/settings.conf`
3. `./settings.conf` (current directory)

### Basic Configuration Sections

#### [rudder] Section
```ini
[rudder]
url = https://your-rudder-server.domain.com
api_token = YOUR-RUDDER-API-TOKEN-HERE
```

**Required Fields:**
- `url`: Full URL to your Rudder server (without trailing slash)
- `api_token`: Rudder API token with read permissions

#### [snipe-it] Section
```ini
[snipe-it]
url = https://your-snipe-instance.domain.com
apikey = YOUR-SNIPE-API-KEY-HERE
manufacturer_id = 2
defaultStatus = 2
computer_model_category_id = 2
```

**Required Fields:**
- `url`: Full URL to your Snipe-IT instance (without trailing slash)
- `apikey`: Snipe-IT API key with create/edit permissions
- `manufacturer_id`: Default manufacturer ID for new assets
- `defaultStatus`: Default status ID for new assets
- `computer_model_category_id`: Category ID for computer models

**Optional Fields:**
- `computer_custom_fieldset_id`: Custom fieldset for computer models
- `asset_tag`: Custom asset tag field (defaults to rudderid-{id})

### Getting Required IDs

#### Manufacturer ID
1. Go to Snipe-IT Admin → Manufacturers
2. Enable the ID column (click column selector)
3. Note the ID of your default manufacturer (usually "Apple" or "Dell")

#### Status ID
1. Go to Snipe-IT Admin → Status Labels  
2. Enable the ID column
3. Note the ID for "Ready to Deploy" or similar status

#### Category ID
1. Go to Snipe-IT Admin → Categories
2. Enable the ID column
3. Note the ID for your computer category

---

## Usage

### Command Line Options

```bash
python3 rudder2snipe [OPTIONS]
```

#### Basic Options
- `-h, --help`: Show help message
- `-v, --verbose`: Enable INFO level logging
- `-d, --debug`: Enable DEBUG level logging
- `--version`: Show version and exit

#### Operation Modes
- `--dryrun`: Test run without making changes
- `--connection_test`: Test API connectivity only
- `-f, --force`: Force update regardless of timestamps
- `--auto_incrementing`: Use Snipe-IT auto-incrementing asset tags

#### Network Options
- `--do_not_verify_ssl`: Skip SSL certificate verification
- `-r, --ratelimited`: Enable rate limiting (120 requests/minute)
- `--do_not_update_rudder`: Don't sync asset tags back to Rudder

#### User Management Options
- `-u, --users`: Check out assets to users if not deployed
- `-ui, --users_inverse`: Check out assets to users if already deployed  
- `-uf, --users_force`: Force check out assets to users
- `-uns, --users_no_search`: Don't search for users if fields don't match

### Basic Usage Examples

#### Test Configuration
```bash
python3 rudder2snipe --connection_test
```

#### Dry Run (No Changes)
```bash
python3 rudder2snipe --dryrun --verbose
```

#### Normal Sync
```bash
python3 rudder2snipe --verbose
```

#### Force Update All Assets
```bash
python3 rudder2snipe --force --verbose
```

#### Rate Limited Sync (Large Instances)
```bash
python3 rudder2snipe --ratelimited --verbose
```

---

## API Mapping

API mapping defines how Rudder node properties map to Snipe-IT asset fields.

### [api-mapping] Section

```ini
[api-mapping]
# Snipe Field = Rudder Property Path
name = hostname
_snipeit_mac_address_1 = properties ipHostNumber
_snipeit_os_2 = properties osName
_snipeit_os_version_3 = properties osVersion
_snipeit_machine_type_4 = properties machineType
_snipeit_manufacturer_5 = properties manufacturer
_snipeit_memory_6 = properties memorySize
```

### Standard Snipe-IT Fields

| Snipe-IT Field | Description | Example Mapping |
|----------------|-------------|-----------------|
| `name` | Asset name | `hostname` |
| `serial` | Serial number | `properties serialNumber` |
| `model_id` | Model (auto-managed) | `properties machineType` |
| `asset_tag` | Asset tag | `properties serialNumber` |

### Custom Fields

Custom fields in Snipe-IT use the format `_snipeit_fieldname_id`:

1. **Get Custom Field DB Name**:
   - Go to Snipe-IT Admin → Custom Fields
   - Enable "DB Field" column
   - Copy the DB field name

2. **Add to Mapping**:
```ini
[api-mapping]
_snipeit_ip_address_1 = properties ipHostNumber
_snipeit_cpu_model_2 = properties processorModel
_snipeit_ram_size_3 = properties memorySize
```

### Available Rudder Properties

Common Rudder node properties you can map:

| Property Path | Description | Example Value |
|---------------|-------------|---------------|
| `hostname` | Node hostname | `server01.example.com` |
| `properties osName` | Operating system | `Ubuntu` |
| `properties osVersion` | OS version | `20.04` |
| `properties osKernelVersion` | Kernel version | `5.4.0-42-generic` |
| `properties machineType` | Hardware model | `PowerEdge R640` |
| `properties manufacturer` | Hardware manufacturer | `Dell Inc.` |
| `properties serialNumber` | Serial number | `1234567890` |
| `properties memorySize` | RAM size | `32768` |
| `properties ipHostNumber` | IP address | `192.168.1.100` |
| `properties timezone` | System timezone | `UTC` |
| `properties agentName` | Rudder agent version | `cfengine-community` |

### Complex Mapping Examples

#### Operating System Information
```ini
[api-mapping]
_snipeit_os_name_1 = properties osName
_snipeit_os_version_2 = properties osVersion
_snipeit_os_kernel_3 = properties osKernelVersion
_snipeit_os_full_4 = properties osFullName
```

#### Hardware Details
```ini
[api-mapping]
_snipeit_cpu_model_5 = properties processorModel
_snipeit_cpu_cores_6 = properties processorCount
_snipeit_ram_size_7 = properties memorySize
_snipeit_disk_size_8 = properties diskSize
```

#### Network Information
```ini
[api-mapping]
_snipeit_ip_address_9 = properties ipHostNumber
_snipeit_mac_address_10 = properties macAddress
_snipeit_hostname_11 = hostname
_snipeit_fqdn_12 = properties fqdn
```

---

## User Management

rudder2snipe can automatically assign assets to users based on Rudder node information.

### [user-mapping] Section

```ini
[user-mapping]
rudder_api_field = properties username
```

### User Assignment Modes

#### Conditional Assignment (`-u, --users`)
- Assigns assets to users **only if** they're not already deployed
- Good for initial setup

```bash
python3 rudder2snipe --users --verbose
```

#### Inverse Assignment (`-ui, --users_inverse`)
- Assigns assets to users **only if** they're already deployed
- Good for reassignments

```bash
python3 rudder2snipe --users_inverse --verbose
```

#### Force Assignment (`-uf, --users_force`)
- Always assigns assets to users regardless of current status
- Overwrites existing assignments

```bash
python3 rudder2snipe --users_force --verbose
```

#### No User Search (`-uns, --users_no_search`)
- Doesn't search Snipe-IT if username doesn't match exactly
- Faster but less flexible

```bash
python3 rudder2snipe --users --users_no_search --verbose
```

### User Mapping Examples

#### Map by Username
```ini
[user-mapping]
rudder_api_field = properties username
```

#### Map by Primary User
```ini
[user-mapping]  
rudder_api_field = properties primaryUser
```

#### Map by Asset Owner
```ini
[user-mapping]
rudder_api_field = properties assetOwner
```

---

## Advanced Configuration

### Custom Asset Tags

By default, rudder2snipe uses `rudderid-{node_id}` for asset tags. You can customize this:

```ini
[snipe-it]
asset_tag = properties serialNumber
```

### Auto-Incrementing Asset Tags

If Snipe-IT has auto-incrementing asset tags enabled:

```bash
python3 rudder2snipe --auto_incrementing
```

### SSL Configuration

#### Skip SSL Verification (Not Recommended)
```bash
python3 rudder2snipe --do_not_verify_ssl
```

#### Custom CA Certificate
```bash
export REQUESTS_CA_BUNDLE=/path/to/ca-certificates.crt
python3 rudder2snipe
```

### Rate Limiting

For large environments or shared Snipe-IT instances:

```bash
python3 rudder2snipe --ratelimited
```

This adds a 0.5-second delay between API calls to stay under 120 requests/minute.

### Logging Configuration

#### Log Levels
- **WARNING** (default): Only errors and warnings
- **INFO** (`--verbose`): Basic operation information  
- **DEBUG** (`--debug`): Detailed debugging information

#### Log to File
```bash
python3 rudder2snipe --verbose 2>&1 | tee rudder2snipe.log
```

---

## Troubleshooting

### Common Issues

#### Authentication Errors

**Problem**: `401 Unauthorized` or `403 Forbidden`

**Solutions**:
1. Verify API tokens are correct
2. Check token permissions
3. Ensure tokens haven't expired

```bash
# Test Rudder API
curl -H "X-API-TOKEN: YOUR-TOKEN" https://rudder.example.com/rudder/api/latest/nodes

# Test Snipe-IT API
curl -H "Authorization: Bearer YOUR-TOKEN" https://snipe.example.com/api/v1/hardware
```

#### Connection Issues

**Problem**: `Connection timeout` or `SSL errors`

**Solutions**:
1. Check network connectivity
2. Verify URLs are correct (no trailing slashes)
3. Test SSL configuration

```bash
# Test connectivity
python3 rudder2snipe --connection_test

# Skip SSL verification (temporary)
python3 rudder2snipe --do_not_verify_ssl --connection_test
```

#### Rate Limiting

**Problem**: `429 Too Many Requests`

**Solutions**:
1. Enable rate limiting
2. Reduce concurrent operations

```bash
python3 rudder2snipe --ratelimited
```

#### Permission Errors

**Problem**: Cannot create assets or models

**Solutions**:
1. Check Snipe-IT user permissions
2. Verify API key scope
3. Test with web interface

#### Data Mapping Issues

**Problem**: Fields not updating or incorrect values

**Solutions**:
1. Verify field names in api-mapping
2. Check custom field DB names
3. Test with debug logging

```bash
python3 rudder2snipe --debug --dryrun
```

### Debug Procedures

#### Full Debug Run
```bash
python3 rudder2snipe --debug --dryrun --verbose
```

#### Test Specific Asset
1. Note asset serial number from logs
2. Check in both Rudder and Snipe-IT
3. Compare field values

#### API Response Testing
```bash
# Test with curl to see raw API responses
curl -H "Authorization: Bearer TOKEN" https://snipe.example.com/api/v1/hardware/byserial/SERIAL
```

---

## Best Practices

### Production Deployment

#### Scheduled Execution
```bash
# Add to crontab for hourly sync
0 * * * * /opt/rudder2snipe/.venv/bin/python /opt/rudder2snipe/rudder2snipe --ratelimited >> /var/log/rudder2snipe.log 2>&1
```

#### Service Account
- Create dedicated service accounts in both systems
- Use least-privilege permissions
- Rotate API keys regularly

#### Monitoring
```bash
# Monitor log files
tail -f /var/log/rudder2snipe.log

# Check for errors
grep -i error /var/log/rudder2snipe.log
```

### Configuration Management

#### Version Control
- Store configuration in version control
- Use templates with variables
- Document all custom mappings

#### Environment Separation
```bash
# Different configs for different environments
/opt/rudder2snipe/
├── settings-prod.conf
├── settings-staging.conf
└── settings-dev.conf
```

#### Backup
- Backup Snipe-IT database before major changes
- Test in staging environment first
- Keep configuration backups

### Performance Optimization

#### Large Environments
- Use rate limiting (`--ratelimited`)
- Run during off-peak hours
- Monitor API response times

#### Selective Syncing
- Filter nodes in Rudder if possible
- Use groups or smart groups
- Implement custom filtering logic

#### Caching
- Cache API responses when possible
- Implement local model lookup cache
- Use conditional requests

---

## Examples

### Example 1: Basic Setup

**Scenario**: Small office with 50 computers

**Configuration**:
```ini
[rudder]
url = https://rudder.company.com
api_token = abc123...

[snipe-it]
url = https://assets.company.com
apikey = xyz789...
manufacturer_id = 1
defaultStatus = 2
computer_model_category_id = 1

[api-mapping]
name = hostname
_snipeit_ip_1 = properties ipHostNumber
_snipeit_os_2 = properties osName
```

**Execution**:
```bash
# Test first
python3 rudder2snipe --connection_test
python3 rudder2snipe --dryrun --verbose

# Live sync
python3 rudder2snipe --verbose
```

### Example 2: Enterprise Setup

**Scenario**: Large organization with 1000+ assets

**Configuration**:
```ini
[rudder]
url = https://rudder.enterprise.com
api_token = enterprise_token...

[snipe-it]
url = https://snipe.enterprise.com  
apikey = enterprise_key...
manufacturer_id = 2
defaultStatus = 4
computer_model_category_id = 3

[api-mapping]
name = hostname
_snipeit_ip_address_1 = properties ipHostNumber
_snipeit_os_name_2 = properties osName
_snipeit_os_version_3 = properties osVersion
_snipeit_cpu_model_4 = properties processorModel
_snipeit_ram_size_5 = properties memorySize
_snipeit_serial_6 = properties serialNumber

[user-mapping]
rudder_api_field = properties primaryUser
```

**Execution**:
```bash
# Rate-limited sync with user assignment
python3 rudder2snipe --ratelimited --users --verbose
```

**Cron Job**:
```bash
# /etc/cron.d/rudder2snipe
0 2 * * * rudder /opt/rudder2snipe/.venv/bin/python /opt/rudder2snipe/rudder2snipe --ratelimited --users >> /var/log/rudder2snipe.log 2>&1
```

### Example 3: Managed Service Provider

**Scenario**: MSP managing multiple client environments

**Directory Structure**:
```
/opt/rudder2snipe/
├── clients/
│   ├── client1/
│   │   └── settings.conf
│   ├── client2/
│   │   └── settings.conf
│   └── client3/
│       └── settings.conf
└── scripts/
    └── sync-all-clients.sh
```

**Sync Script**:
```bash
#!/bin/bash
# sync-all-clients.sh

for client in /opt/rudder2snipe/clients/*/; do
    echo "Syncing $(basename $client)..."
    cd "$client"
    /opt/rudder2snipe/.venv/bin/python /opt/rudder2snipe/rudder2snipe --ratelimited --verbose
done
```

### Example 4: Development Environment

**Scenario**: Development/testing setup

**Configuration**:
```ini
[rudder]
url = https://rudder-dev.lab.local
api_token = dev_token...

[snipe-it]
url = https://snipe-dev.lab.local
apikey = dev_key...
manufacturer_id = 1
defaultStatus = 1
computer_model_category_id = 1

[api-mapping]
name = hostname
_snipeit_environment_1 = properties environment
```

**Usage**:
```bash
# Always use dry run in development
python3 rudder2snipe --dryrun --debug

# Test specific features
python3 rudder2snipe --dryrun --users_force --debug
```

---

## Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

### Quick Contribution Steps

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

### Development Setup

```bash
git clone https://github.com/YOUR-USERNAME/rudder2snipe.git
cd rudder2snipe
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### Testing

```bash
# Syntax check
python3 -m py_compile rudder2snipe

# Lint check
flake8 rudder2snipe

# Test run
python3 rudder2snipe --help
```

---

## FAQ

### General Questions

**Q: How often should I run rudder2snipe?**
A: Depends on your needs. Most organizations run it hourly or daily. Use `--ratelimited` for frequent runs.

**Q: Can I run multiple instances simultaneously?**
A: Not recommended. API rate limits and database conflicts may occur.

**Q: Does rudder2snipe delete assets?**
A: No, it only creates and updates assets. Manual cleanup may be needed for decommissioned nodes.

### Technical Questions

**Q: What happens if Rudder and Snipe-IT have conflicting data?**
A: rudder2snipe compares timestamps and updates the asset only if Rudder has newer data. Use `--force` to override.

**Q: Can I map one Rudder field to multiple Snipe-IT fields?**
A: Not directly. You would need custom logic or multiple mapping entries.

**Q: How do I handle custom asset tags?**
A: Set `asset_tag` in the `[snipe-it]` section to map to a Rudder property, or use `--auto_incrementing`.

### Troubleshooting Questions

**Q: Why aren't my custom fields updating?**
A: Check the DB field name in Snipe-IT Admin → Custom Fields. Ensure it matches your mapping exactly.

**Q: I'm getting SSL errors, what should I do?**
A: Try `--do_not_verify_ssl` temporarily, but properly configure SSL certificates for production.

**Q: Assets are being created but not updated. Why?**
A: Check timestamps. Use `--force` to override timestamp checking, or ensure Rudder inventory is up to date.

---

## Credits

### Original Project
This project is based on the excellent [jamf2snipe](https://github.com/grokability/jamf2snipe) project by Brian Monroe and contributors.

### Acknowledgments
- **Brian Monroe** and the jamf2snipe contributors for the original foundation
- **Rudder.io team** for the excellent configuration management platform  
- **Snipe-IT team** for the robust asset management system
- **Python community** for the libraries that make this possible

### License
MIT License - see [LICENSE](LICENSE) file for details.

---

## Support

- **Issues**: [GitHub Issues](https://github.com/norbertoaquino/rudder2snipe/issues)
- **Discussions**: [GitHub Discussions](https://github.com/norbertoaquino/rudder2snipe/discussions)
- **Documentation**: [Project Wiki](https://github.com/norbertoaquino/rudder2snipe/wiki)

### Getting Help

1. Check this documentation first
2. Search existing issues
3. Create a detailed issue with:
   - Environment details
   - Configuration (sanitized)
   - Error messages
   - Steps to reproduce

---

*Last updated: December 2024*
