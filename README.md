# Azure Networking Foundations – Three-Tier Network Architecture

## Project Overview

This project demonstrates the design and configuration of a segmented three-tier network architecture in Microsoft Azure.

The goal was to build a networking foundation that separates web, application, and data workloads into dedicated subnets and applies Network Security Groups (NSGs) to control traffic between the different network tiers.

The lab focuses on core Azure networking concepts including:

- Resource Groups
- Virtual Networks (VNets)
- IP address spaces
- Subnet segmentation
- Network Security Groups (NSGs)
- Inbound security rules
- NSG-to-subnet associations
- Network traffic restrictions
- Resource visualization
- Azure resource lifecycle and cleanup

---

## Architecture

The environment was built inside the following Azure resource group:

`rg-cloudfirst-network`

A Virtual Network named:

`vnet-cloudfirst-dev`

was configured with three separate subnets representing a traditional three-tier application architecture.

| Tier | Subnet | Address Range | Network Security Group |
|---|---|---|---|
| Web | `snet-web` | `10.0.1.0/24` | `nsg-web` |
| Application | `snet-app` | `10.0.2.0/24` | `nsg-app` |
| Data | `snet-data` | `10.0.3.0/24` | `nsg-data` |

Conceptually, the traffic flow is:

Internet  
↓  
Web Tier (`snet-web`)  
↓ TCP 8080  
Application Tier (`snet-app`)  
↓ TCP 1433  
Data Tier (`snet-data`)

This design demonstrates network segmentation rather than placing all application components inside a single subnet.

---

## Network Security Groups

Three Network Security Groups were created so that each network tier could have its own traffic-control policy.

### Web Tier – `nsg-web`

The web-tier NSG was configured to allow inbound web traffic.

Custom inbound rules included:

- HTTP – TCP port `80`
- HTTPS – TCP port `443`

This represents the externally accessible layer of the architecture.

### Application Tier – `nsg-app`

The application tier was configured to accept application traffic from the web subnet.

Rule:

`Allow-Web-to-App`

Configuration:

- Source: `10.0.1.0/24`
- Destination port: `8080`
- Protocol: TCP
- Action: Allow
- Priority: 100

This restricts the application-tier rule to traffic originating from the web subnet rather than allowing unrestricted inbound access.

### Data Tier – `nsg-data`

The data tier was configured to accept SQL traffic from the application subnet.

Rule:

`Allow-App-to-Data`

Configuration:

- Source: `10.0.2.0/24`
- Destination port: `1433`
- Protocol: TCP
- Action: Allow
- Priority: 100

This demonstrates how the data tier can be isolated from direct web-tier access while still permitting the application tier to communicate with it.

---

## NSG and Subnet Associations

Each Network Security Group was associated with its corresponding subnet:

- `nsg-web` → `snet-web`
- `nsg-app` → `snet-app`
- `nsg-data` → `snet-data`

This allows security policies to be applied independently to each network segment.

---

## Security Design

The architecture follows a layered approach to network security.

Instead of allowing every network segment to communicate freely, traffic is permitted according to the role of each tier.

The intended application path is:

`Web → Application → Data`

Examples implemented in the lab include:

`10.0.1.0/24 → TCP 8080 → Application Tier`

and:

`10.0.2.0/24 → TCP 1433 → Data Tier`

Azure's default NSG rules remain present alongside the custom rules configured during the lab.

This project therefore demonstrates the fundamentals of using subnet segmentation and NSGs to build more controlled network boundaries in Azure.

---

## Project Structure

```text
azure-networking-foundations-lab/
│
├── README.md
├── KEY-LEARNINGS.md
│
└── Screenshots/
    │
    ├── 01-resource-group/
    ├── 02-virtual-network/
    ├── 03-subnets/
    ├── 04-network-security-group/
    ├── 05-nsg-rules/
    └── 06-network-review/