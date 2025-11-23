# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2025-11-23

### Added
- Initial release of VPC module
- Support for creating Google Compute Network (VPC)
- Configurable network routing mode (GLOBAL/REGIONAL)
- Support for auto-created subnetworks or custom subnet mode
- Shared VPC host project configuration
- Network resource outputs including name, ID, and self-link
- Full VPC resource output for advanced use cases

### Features
- **VPC Creation**: Create custom VPC networks with configurable settings
- **Routing Configuration**: Support for both GLOBAL and REGIONAL routing modes
- **Subnet Management**: Choice between auto-created or custom subnet mode
- **Shared VPC**: Enable Shared VPC host project functionality
- **Resource Outputs**: Comprehensive outputs for network integration

### Outputs
- `network` - Complete VPC resource object
- `network_name` - Name of the created VPC
- `network_id` - ID of the created VPC
- `network_self_link` - Self-link URI of the VPC

### Requirements
- Terraform >= 1.5.0
- Google Provider >= 7.0.0, < 8.0.0
- Google Beta Provider >= 7.0.0, < 8.0.0