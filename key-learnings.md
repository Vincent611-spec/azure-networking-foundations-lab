# Key Learnings – Azure Networking Foundations

## Overview

This project helped me move beyond simply knowing what Azure networking services are and begin understanding how they work together when designing a cloud network.

By building a segmented three-tier network architecture in Microsoft Azure, I gained practical experience with Virtual Networks, subnetting, IP address planning, Network Security Groups, security rules, traffic flow, and network segmentation.

The biggest lesson from this project was that cloud networking is not simply about connecting resources. It is also about deciding **which resources should communicate, how they should communicate, and where security boundaries should exist.**

---

# 1. Azure Virtual Networks Provide the Network Foundation

One of the first concepts reinforced during this project was the role of an Azure Virtual Network (VNet).

A VNet provides a private networking environment where Azure resources can be placed and communicate.

For this project, I created:

`vnet-cloudfirst-dev`

Rather than treating the VNet as one flat network, I divided it into multiple subnets representing different application functions.

This helped me understand that creating the VNet itself is only the beginning of network design.

The next important decisions involve:

- IP address planning
- Subnet design
- Workload separation
- Traffic requirements
- Security boundaries

---

# 2. Subnetting Creates Logical Network Boundaries

I created three dedicated subnets:

| Tier | Subnet | Address Range |
|---|---|---|
| Web | `snet-web` | `10.0.1.0/24` |
| Application | `snet-app` | `10.0.2.0/24` |
| Data | `snet-data` | `10.0.3.0/24` |

Before this project, it was easy to think of a subnet mainly as a range of IP addresses.

The lab helped me understand its architectural purpose more clearly.

Subnets allow different types of workloads to be separated into logical network segments.

In this design:

```text
Web Tier
   ↓
Application Tier
   ↓
Data Tier
```

Each tier has a different purpose and therefore can have different security requirements.

This separation makes the network easier to organize and provides boundaries where security policies can be applied.

---

# 3. CIDR Planning Is Important Before Creating Resources

The project also gave me practical experience working with CIDR notation.

The subnet ranges used were:

```text
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
```

Using separate, non-overlapping address ranges allowed each application tier to have its own network segment.

This helped reinforce that IP address planning should be considered during the design stage rather than after resources have already been deployed.

I also became more comfortable reading CIDR ranges and understanding how they identify different network segments.

---

# 4. Network Security Groups Control Traffic

A major part of the project involved Azure Network Security Groups (NSGs).

I created:

- `nsg-web`
- `nsg-app`
- `nsg-data`

An NSG contains security rules that determine whether network traffic should be allowed or denied.

While configuring the rules, I worked with properties including:

- Source
- Destination
- Source port
- Destination port
- Protocol
- Action
- Priority

This helped me understand that an NSG is not simply something that is attached to a network.

Its rules define the actual traffic-control policy.

---

# 5. Security Policies Can Be Applied to Individual Subnets

Each NSG was associated with its corresponding subnet:

| Network Security Group | Subnet |
|---|---|
| `nsg-web` | `snet-web` |
| `nsg-app` | `snet-app` |
| `nsg-data` | `snet-data` |

This was an important part of the project because it demonstrated how different network segments can have their own security policies.

Instead of applying one identical security configuration everywhere, each tier can be protected according to its role.

For example:

- The Web tier can accept web-related traffic.
- The Application tier can accept traffic originating from the Web tier.
- The Data tier can accept traffic originating from the Application tier.

This helped me understand the relationship between **network segmentation and security policy**.

---

# 6. NSG Rule Priority Matters

While configuring custom NSG rules, I learned that Azure evaluates NSG rules according to their priority values.

A lower numerical value represents a higher priority.

For example:

`Priority 100`

is evaluated before rules with higher numerical priority values.

The custom Web-to-Application and Application-to-Data rules in this lab used priority `100`.

This reinforced the importance of considering rule order when designing network security policies.

Creating the correct rule is not enough; its priority also affects how traffic is evaluated.

---

# 7. Source Networks Can Be Used to Restrict Traffic

One of the most useful lessons from the project was learning how traffic can be restricted according to its source network.

For the Application tier, I configured:

`Allow-Web-to-App`

with:

- Source: `10.0.1.0/24`
- Destination port: `8080`
- Protocol: TCP
- Action: Allow

Conceptually:

```text
snet-web
10.0.1.0/24
     │
     │ TCP 8080
     ▼
snet-app
```

Rather than allowing TCP 8080 from any source, the rule was designed specifically around traffic originating from the Web subnet.

This showed me how network rules can become more specific and controlled.

---

# 8. The Data Tier Can Be Isolated from Direct Web-Tier Traffic

For the Data tier, I configured:

`Allow-App-to-Data`

with:

- Source: `10.0.2.0/24`
- Destination port: `1433`
- Protocol: TCP
- Action: Allow

Conceptually:

```text
snet-app
10.0.2.0/24
     │
     │ TCP 1433
     ▼
snet-data
```

TCP port `1433` was used in this lab to represent Microsoft SQL Server traffic.

The important lesson was not simply remembering the port number.

The more important concept was understanding the intended communication path:

```text
Web
 ↓
Application
 ↓
Data
```

The design demonstrates why a backend data tier does not need to be treated the same way as a web-facing tier.

---

# 9. A Three-Tier Architecture Separates Responsibilities

This project gave me a much clearer understanding of the networking concept behind three-tier application architecture.

### Web Tier

Represents the layer that would receive web-facing traffic.

In this lab, HTTP and HTTPS rules were configured using:

- TCP `80`
- TCP `443`

### Application Tier

Represents the application or business-logic layer.

The intended traffic path from the Web tier used:

- TCP `8080`

### Data Tier

Represents the backend data layer.

The intended Application-to-Data traffic used:

- TCP `1433`

The conceptual architecture was:

```text
Internet
   │
   │ HTTP/HTTPS (80/443)
   ▼
Web Tier
   │
   │ TCP 8080
   ▼
Application Tier
   │
   │ TCP 1433
   ▼
Data Tier
```

The project focused on creating the **networking foundation** for this architecture.

Actual web servers, application servers, and databases were not deployed.

This distinction was important because it helped me separate **network architecture design** from **application deployment**.

---

# 10. Default and Custom NSG Rules Work Together

Azure automatically provides default rules when an NSG is created.

During the lab, I added custom rules to represent the traffic required by the network design.

I learned that adding custom rules does not simply remove Azure's default rules.

Instead, traffic evaluation depends on the applicable rules and their priorities.

This made NSG configuration easier to understand because I could see the difference between:

- Azure-provided default behavior
- Security rules intentionally created for the architecture

---

# 11. Network Security Is About Controlling Communication Paths

One of the most important lessons from this project was that network security is not only about opening or closing ports.

It is also about understanding:

- Where traffic originates
- Where traffic should go
- Which protocol is required
- Which port is required
- Which network tier should receive the traffic
- Whether that communication is actually necessary

For example, instead of thinking:

> "Port 1433 needs to be open."

I learned to think more specifically:

> "The Data tier should accept TCP 1433 traffic from the Application subnet for the intended application-to-data communication path."

That is a much more useful way of thinking about network security.

---

# 12. Resource Visualizer Helps Validate the Architecture

After configuring the network, I used Azure Resource Visualizer to review the relationships between the resources.

The visualizer showed the relationship between:

- `vnet-cloudfirst-dev`
- `nsg-web`
- `nsg-app`
- `nsg-data`

This taught me that validation should not stop after clicking **Create** or seeing a successful deployment notification.

It is useful to review the completed environment and confirm that resources are connected and associated as intended.

I also reviewed the subnet and NSG configuration pages to verify the architecture.

---

# 13. Validation Is Part of the Deployment Process

Before considering the lab complete, I checked:

- The three subnets existed
- The subnet address ranges were correct
- Each NSG existed
- Each NSG was associated with the intended subnet
- Custom inbound security rules were present
- The network relationships appeared correctly in Resource Visualizer

This reinforced an important operational lesson:

**Creating a resource and verifying that it is configured correctly are two different steps.**

Validation should be part of the deployment process.

---

# 14. Resource Cleanup Is Part of Cloud Administration

After completing the project, validating the environment, and capturing the required screenshots, I deleted:

`rg-cloudfirst-network`

Because the project resources were contained within the Resource Group, deleting the Resource Group removed the networking resources created specifically for the lab.

This reinforced the importance of resource lifecycle management:

```text
Plan
  ↓
Deploy
  ↓
Configure
  ↓
Validate
  ↓
Document
  ↓
Clean Up
```

Temporary lab resources should not remain deployed unnecessarily.

Cleaning them up keeps the Azure environment organized and helps reduce the possibility of unnecessary cloud costs.

---

# Practical Lessons Learned

Beyond the individual Azure services, this project helped me understand several broader cloud infrastructure concepts.

### Plan the network before deploying resources

Subnet ranges, network tiers, and expected traffic paths should be considered during the design stage.

### Separate workloads according to function

Web, application, and data workloads can have different networking and security requirements.

### Avoid unnecessarily broad traffic rules

A security rule can specify both the required port and the expected source network instead of allowing traffic from everywhere.

### Understand the traffic path

Before creating a security rule, it is important to understand:

```text
Source → Destination → Protocol → Port → Action
```

### Validate after configuration

A successful deployment does not automatically mean that every relationship or security rule is configured as intended.

### Clean up temporary resources

Resource cleanup is part of managing cloud infrastructure responsibly.

---

# Challenges and Problem Solving

This project also involved several areas where I had to think carefully about the configuration rather than simply creating resources.

## Designing the subnet structure

I needed to understand how the Web, Application, and Data tiers should be separated and assign each tier its own address range.

This reinforced the relationship between IP address planning and network architecture.

## Understanding NSG associations

Creating an NSG alone does not establish the intended subnet-level security design.

I needed to associate each NSG with the correct subnet and later verify those associations.

## Designing source-specific rules

For the Application and Data tiers, I needed to think about where traffic should originate rather than simply allowing a destination port.

This helped me better understand controlled traffic flow between network segments.

## Reviewing the completed environment

Using the Azure Portal and Resource Visualizer helped me confirm that the final network matched the intended design.

---

# Skills Developed

Through this project, I gained practical experience with:

- Azure Virtual Networks
- IPv4 address planning
- CIDR notation
- Subnet creation
- Network segmentation
- Three-tier network architecture
- Network Security Groups
- NSG rule configuration
- NSG rule priorities
- TCP ports
- Source-based traffic restrictions
- NSG-to-subnet associations
- Network traffic-flow planning
- Azure Resource Visualizer
- Configuration validation
- Resource Groups
- Azure resource cleanup

---

# What I Can Explain After This Project

After completing this project, I can explain:

- What an Azure Virtual Network is
- Why VNets are divided into subnets
- What CIDR notation represents
- Why different workloads can be placed in separate network segments
- What a Network Security Group does
- How inbound NSG rules work
- Why NSG rule priority matters
- How an NSG can be associated with a subnet
- How source networks can be used to restrict traffic
- The purpose of Web, Application, and Data tiers
- Why HTTP and HTTPS commonly use ports 80 and 443
- How TCP 8080 was used for Web-to-Application traffic in this lab
- How TCP 1433 was used for Application-to-Data traffic in this lab
- Why traffic rules should reflect the intended communication path
- How Resource Visualizer can help validate Azure resource relationships
- Why temporary Azure resources should be cleaned up after a lab

---

# Personal Reflection

This project made Azure networking feel much more practical to me.

Before building the lab, concepts such as VNets, subnets, NSGs, CIDR ranges, ports, and network segmentation could be understood individually.

Building the environment helped me see how those concepts connect.

I now better understand that designing a cloud network involves more than creating a VNet. It requires thinking about **addressing, segmentation, traffic flow, security boundaries, validation, and resource lifecycle management together.**

The most valuable part of the project was designing the communication path between the three tiers:

```text
Internet
   │
   │ HTTP/HTTPS (80/443)
   ▼
Web Tier
   │
   │ TCP 8080
   ▼
Application Tier
   │
   │ TCP 1433
   ▼
Data Tier
```

That made the relationship between network architecture and security rules much clearer.

This project gives me a stronger networking foundation that I can build on in future Azure labs involving virtual machines, private connectivity, routing, load balancing, monitoring, and other infrastructure services.

---

## Related Documentation

For the full project implementation, architecture, screenshots, and configuration details:

[← Back to Project README](README.md)
