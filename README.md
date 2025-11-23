# tfmodule-google-vpc

Terraform module for creating and managing Google Cloud VPC (Virtual Private Cloud) networks with support for custom routing, subnet configuration, and Shared VPC functionality.

## Features

- **Custom VPC Creation**: Create VPC networks with configurable settings
- **Flexible Routing**: Support for both GLOBAL and REGIONAL routing modes
- **Subnet Configuration**: Choose between auto-created or custom subnet mode
- **Shared VPC Support**: Enable Shared VPC host project for multi-project architectures
- **Comprehensive Outputs**: Access network details for integration with other resources

## Usage

### Basic Example

Create a simple custom VPC with global routing:

```hcl
module "vpc" {
  source = "git::https://github.com/your-org/tfmodule-google-vpc.git?ref=v1.0.0"

  project_id   = "my-gcp-project"
  network_name = "my-custom-vpc"
}
```

### Custom VPC with Regional Routing

Create a VPC with regional routing mode:

```hcl
module "vpc" {
  source = "git::https://github.com/your-org/tfmodule-google-vpc.git?ref=v1.0.0"

  project_id   = "my-gcp-project"
  network_name = "regional-vpc"
  routing_mode = "REGIONAL"
}
```

### Auto-Created Subnetworks

Create a VPC with automatically created subnetworks in each region:

```hcl
module "vpc" {
  source = "git::https://github.com/your-org/tfmodule-google-vpc.git?ref=v1.0.0"

  project_id              = "my-gcp-project"
  network_name            = "auto-subnet-vpc"
  auto_create_subnetworks = true
}
```

### Shared VPC Host Project

Configure a project as a Shared VPC host:

```hcl
module "shared_vpc" {
  source = "git::https://github.com/your-org/tfmodule-google-vpc.git?ref=v1.0.0"

  project_id      = "host-project-id"
  network_name    = "shared-vpc-network"
  shared_vpc_host = true
}
```

### Complete Example

Full configuration with all options:

```hcl
module "vpc" {
  source = "git::https://github.com/your-org/tfmodule-google-vpc.git?ref=v1.0.0"

  project_id              = "my-gcp-project"
  network_name            = "production-vpc"
  routing_mode            = "GLOBAL"
  auto_create_subnetworks = false
  shared_vpc_host         = false
}

# Use outputs to create subnets
resource "google_compute_subnetwork" "subnet" {
  name          = "production-subnet"
  ip_cidr_range = "10.0.1.0/24"
  region        = "us-central1"
  network       = module.vpc.network_id
  project       = "my-gcp-project"
}
```

### Enterprise Multi-Region VPC

Create an enterprise-grade VPC for multi-region deployment:

```hcl
module "enterprise_vpc" {
  source = "git::https://github.com/your-org/tfmodule-google-vpc.git?ref=v1.0.0"

  project_id              = "enterprise-prod"
  network_name            = "enterprise-global-vpc"
  routing_mode            = "GLOBAL"
  auto_create_subnetworks = false
  shared_vpc_host         = true
}

# Create regional subnets
resource "google_compute_subnetwork" "us_central" {
  name          = "us-central1-subnet"
  ip_cidr_range = "10.1.0.0/20"
  region        = "us-central1"
  network       = module.enterprise_vpc.network_id
  project       = "enterprise-prod"

  secondary_ip_range {
    range_name    = "pods"
    ip_cidr_range = "10.4.0.0/14"
  }

  secondary_ip_range {
    range_name    = "services"
    ip_cidr_range = "10.8.0.0/20"
  }
}

resource "google_compute_subnetwork" "us_east" {
  name          = "us-east1-subnet"
  ip_cidr_range = "10.2.0.0/20"
  region        = "us-east1"
  network       = module.enterprise_vpc.network_id
  project       = "enterprise-prod"
}

resource "google_compute_subnetwork" "europe_west" {
  name          = "europe-west1-subnet"
  ip_cidr_range = "10.3.0.0/20"
  region        = "europe-west1"
  network       = module.enterprise_vpc.network_id
  project       = "enterprise-prod"
}
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| project_id | The ID of the project where this VPC will be created | `string` | n/a | yes |
| network_name | The name of the network being created | `string` | n/a | yes |
| routing_mode | The network routing mode (default 'GLOBAL') | `string` | `"GLOBAL"` | no |
| auto_create_subnetworks | When set to true, the network is created in 'auto subnet mode' and it will create a subnet for each region automatically across the 10.128.0.0/9 address range. When set to false, the network is created in 'custom subnet mode' so the user can explicitly connect subnetwork resources | `bool` | `false` | no |
| shared_vpc_host | Makes this project a Shared VPC host if 'true' (default 'false') | `bool` | `false` | no |

## Outputs

| Name | Description |
|------|-------------|
| network | The VPC resource being created |
| network_name | The name of the VPC being created |
| network_id | The ID of the VPC being created |
| network_self_link | The URI of the VPC being created |

## Requirements

| Name | Version |
|------|---------|
| terraform | >= 1.5.0 |
| google | >= 7.0.0, < 8.0.0 |
| google-beta | >= 7.0.0, < 8.0.0 |

## Notes

- **Custom Subnet Mode**: When `auto_create_subnetworks` is set to `false` (default), you must manually create subnetworks. This provides more control over IP ranges and regional deployment.
- **Auto Subnet Mode**: When `auto_create_subnetworks` is set to `true`, Google Cloud automatically creates one subnet per region using the 10.128.0.0/9 address range.
- **Routing Mode**: 
  - `GLOBAL`: Routes are learned and shared across all regions
  - `REGIONAL`: Routes are learned and applied only within the region where they are created
- **Shared VPC**: Enabling `shared_vpc_host` allows other projects to use this VPC network, enabling centralized network management in multi-project environments.

## Changelog

See [CHANGELOG.md](./CHANGELOG.md) for version history and changes.