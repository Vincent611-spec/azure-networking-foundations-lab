# Azure Networking Foundations – Three-Tier Network Architecture

## Project Overview

This project is a hands-on Microsoft Azure networking lab focused on designing and configuring a segmented three-tier network architecture.

Rather than placing all workloads inside a single network segment, I created a Virtual Network (VNet) with separate subnets representing the:

- Web tier
- Application tier
- Data tier

I then created and associated dedicated Network Security Groups (NSGs) with each subnet and configured custom inbound security rules to demonstrate controlled traffic flow between the different network tiers.

The project focuses on practical Azure networking concepts including Virtual Networks, subnetting, IP address planning, Network Security Groups, security rules, network segmentation, and resource organization.

---

# Why I Built This Project

As part of my Microsoft Azure learning journey, I wanted to move beyond understanding networking concepts only from theory and gain practical experience configuring them directly in the Azure Portal.

Networking is a fundamental part of cloud infrastructure because resources need to communicate while still being appropriately separated and protected.

This lab allowed me to practice designing a simple three-tier network and understand how Azure Network Security Groups can be used to control traffic between different parts of an environment.

---

# Objectives

The objectives of this project were to:

- Create and organize Azure networking resources inside a Resource Group
- Deploy an Azure Virtual Network
- Plan and configure an IP address space
- Segment the VNet into multiple subnets
- Create separate Web, Application, and Data network tiers
- Create Network Security Groups for each tier
- Configure custom inbound NSG security rules
- Restrict application-tier traffic to the web subnet
- Restrict data-tier traffic to the application subnet
- Associate NSGs with their corresponding subnets
- Review the completed network architecture using Azure Resource Visualizer
- Practice Azure resource cleanup after completing the lab

---

# Technologies Used

- Microsoft Azure Portal
- Azure Resource Groups
- Azure Virtual Network (VNet)
- Azure Subnets
- Azure Network Security Groups (NSGs)
- Azure Resource Visualizer

---

# Skills Demonstrated

This project demonstrates practical experience with:

- Azure Portal navigation
- Virtual Network configuration
- IPv4 address planning
- Subnet segmentation
- Network Security Groups
- Inbound security rules
- Source-based traffic filtering
- TCP port configuration
- NSG-to-subnet associations
- Three-tier network architecture
- Network segmentation
- Basic cloud network security
- Azure resource organization
- Azure resource lifecycle management

---

# Architecture

The networking environment was created inside the following Azure Resource Group:

`rg-cloudfirst-network`

A Virtual Network named:

`vnet-cloudfirst-dev`

was configured with three dedicated subnets.

| Tier | Subnet | Address Range | Network Security Group |
|---|---|---|---|
| Web | `snet-web` | `10.0.1.0/24` | `nsg-web` |
| Application | `snet-app` | `10.0.2.0/24` | `nsg-app` |
| Data | `snet-data` | `10.0.3.0/24` | `nsg-data` |

Conceptually, the intended application traffic path is:

Internet
   │
   │ HTTP/HTTPS (80/443)
   ▼
Web Tier (snet-web)
   │
   │ TCP 8080
   ▼
Application Tier (snet-app)
   │
   │ TCP 1433
   ▼
Data Tier (snet-data)

This design demonstrates the principle of network segmentation by separating different application functions into dedicated network boundaries.

> **Note:** This lab focused on building the networking foundation. Application servers, databases, and production workloads were not deployed.

---

# Project Structure

    azure-networking-foundations-lab/
    │
    ├── README.md
    ├── key-learnings.md
    │
    └── Screenshots/
        │
        ├── 01-resource-group/
        ├── 02-virtual-network/
        ├── 03-subnets/
        ├── 04-network-security-group/
        ├── 05-nsg-rules/
        └── 06-network-review/

---

# Project Walkthrough

## Part 1 — Resource Group

Created a dedicated Azure Resource Group:

`rg-cloudfirst-network`

The Resource Group provided a logical container for organizing the networking resources used throughout the lab.

Resources created inside the group included:

- Virtual Network
- Network Security Groups
- Subnet configurations

Tags were also used to provide additional organization and context for the environment.

---

## Part 2 — Virtual Network

Created the Virtual Network:

`vnet-cloudfirst-dev`

The VNet provides the private network boundary in which the three network tiers were designed.

During this stage, I worked with:

- VNet configuration
- Azure regions
- IPv4 address spaces
- Subnet planning
- Network validation

The address space was divided into smaller subnet ranges so that different workload tiers could be logically separated.

---

## Part 3 — Subnet Segmentation

The Virtual Network was segmented into three `/24` subnets.

### Web Subnet

`10.0.1.0/24`

Represents the web-facing tier of the architecture.

### Application Subnet

`10.0.2.0/24`

Represents the application/business logic tier.

### Data Subnet

`10.0.3.0/24`

Represents the backend data tier.

Separating these tiers into individual subnets provides clearer network boundaries and allows security policies to be applied independently.

---

## Part 4 — Network Security Groups

Three Network Security Groups were created:

- `nsg-web`
- `nsg-app`
- `nsg-data`

Each NSG represents the traffic-control policy for a specific network tier.

The NSGs were then associated with their corresponding subnets:

| Network Security Group | Associated Subnet |
|---|---|
| `nsg-web` | `snet-web` |
| `nsg-app` | `snet-app` |
| `nsg-data` | `snet-data` |

This allows security rules to be applied independently to each network segment.

---

## Part 5 — Web Tier Security Rules

The Web tier was configured to demonstrate inbound web access.

Custom inbound rules were created for:

- HTTP — TCP port `80`
- HTTPS — TCP port `443`

These rules represent the type of inbound traffic typically required by an internet-facing web tier.

The lab did not deploy an actual public web workload; these rules were configured to demonstrate the network security design.

---

## Part 6 — Web-to-Application Traffic

The Application tier was configured with the custom rule:

`Allow-Web-to-App`

Configuration:

- Source: `10.0.1.0/24`
- Source tier: Web subnet
- Destination port: `8080`
- Protocol: TCP
- Action: Allow
- Priority: `100`

This rule demonstrates how application traffic can be limited to requests originating from the web subnet rather than permitting unrestricted inbound access.

Conceptually:

`Web Tier → TCP 8080 → Application Tier`

---

## Part 7 — Application-to-Data Traffic

The Data tier was configured with the custom rule:

`Allow-App-to-Data`

Configuration:

- Source: `10.0.2.0/24`
- Source tier: Application subnet
- Destination port: `1433`
- Protocol: TCP
- Action: Allow
- Priority: `100`

TCP port `1433` is commonly associated with Microsoft SQL Server traffic.

This configuration demonstrates how a data tier can be separated from direct web-tier access while still permitting communication from the application tier.

Conceptually:

`Application Tier → TCP 1433 → Data Tier`

---

# Security Design

The architecture follows a layered approach to network security.

Instead of treating the entire VNet as one unrestricted network, the environment was divided according to workload function:

`Web → Application → Data`

The custom rules demonstrate more specific communication paths:

`10.0.1.0/24 → TCP 8080 → Application Tier`

and:

`10.0.2.0/24 → TCP 1433 → Data Tier`

This helped demonstrate an important networking principle:

**Resources should only be allowed to communicate over the network paths and ports required for their intended function.**

Azure's default NSG rules remained present alongside the custom rules configured during the lab.

---

# Network Review and Validation

After configuring the environment, I reviewed the completed network design from multiple areas of the Azure Portal.

I verified:

- All three subnets existed
- Each subnet used the intended address range
- Each subnet was associated with the correct NSG
- All three Network Security Groups existed
- Custom inbound rules were present
- The VNet and NSG relationships appeared correctly in Azure Resource Visualizer

The Resource Visualizer provided a useful graphical representation of the relationship between:

- `vnet-cloudfirst-dev`
- `nsg-web`
- `nsg-app`
- `nsg-data`

---

# Screenshots

The repository includes screenshots documenting the major stages of the project.

## Resource Group

`Screenshots/01-resource-group/`

Documents the Resource Group configuration and overview.

## Virtual Network

`Screenshots/02-virtual-network/`

Documents VNet design, validation, and deployment.

## Subnets

`Screenshots/03-subnets/`

Documents the completed three-tier subnet configuration.

## Network Security Groups

`Screenshots/04-network-security-group/`

Documents NSG creation and subnet association.

## NSG Rules

`Screenshots/05-nsg-rules/`

Documents:

- HTTP rule
- HTTPS rule
- Web-to-Application rule
- Application-to-Data rule

## Network Review

`Screenshots/06-network-review/`

Documents:

- Subnet-to-NSG associations
- Network Security Group overview
- Azure Resource Visualizer

---

# What I Learned

This project strengthened my practical understanding of Azure networking fundamentals.

I gained hands-on experience with:

- Creating Azure Virtual Networks
- Planning subnet address ranges
- Segmenting networks according to workload function
- Creating and configuring Network Security Groups
- Understanding NSG rule priorities
- Working with source IP/CIDR ranges
- Configuring TCP-based security rules
- Associating NSGs with subnets
- Understanding traffic flow between network tiers
- Reviewing Azure network relationships visually
- Cleaning up Azure resources after completing a lab

Most importantly, the project helped me understand that creating a VNet is only one part of cloud networking.

A well-designed network also requires thinking about **segmentation, traffic flow, security boundaries, and which systems should be allowed to communicate with each other.**

For a more detailed reflection on the concepts learned during this project:

➡️ **[View Key Learnings](key-learnings.md)**

---

# Resource Cleanup

After documenting and validating the completed architecture, the lab Resource Group was deleted.

Deleting:

`rg-cloudfirst-network`

removed the resources created specifically for this project, including:

- `vnet-cloudfirst-dev`
- `nsg-web`
- `nsg-app`
- `nsg-data`

This was done to practice proper Azure resource lifecycle management and avoid leaving unnecessary lab resources in the subscription.

---

# Future Improvements

This project focused specifically on the networking foundation.

A future version of this architecture could be expanded by adding:

- Virtual Machines to the Web and Application tiers
- Azure SQL or another database service for the Data tier
- Azure Bastion for secure administrative access
- Azure Load Balancer
- NAT Gateway
- Route Tables and custom routes
- Private Endpoints
- Azure DNS
- Network Watcher
- NSG Flow Logs and network monitoring
- Infrastructure as Code using Bicep or Terraform

These additions would allow the networking foundation created in this project to support a more complete Azure application environment.

---

# About Me

I'm building practical Microsoft Azure projects to develop hands-on cloud administration and infrastructure skills alongside my Azure studies.

My goal is to progressively move from Azure fundamentals into deeper administration, networking, identity, security, monitoring, and infrastructure management.

This repository is part of my growing Azure portfolio documenting that practical learning journey.

---

## Author

**Vincent Ekekwe**

Aspiring Cloud Administrator | IT Support Engineer

Microsoft Azure (AZ-900 → AZ-104)
