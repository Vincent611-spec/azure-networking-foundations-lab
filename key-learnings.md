
---

## 2. `KEY-LEARNINGS.md`

This one is especially useful because it captures what you should actually be able to explain when someone asks about the project.

```markdown
# Key Learnings – Azure Networking Foundations

## 1. A Virtual Network Is the Main Private Network Boundary

I learned that an Azure Virtual Network (VNet) provides the private networking environment in which Azure resources can communicate.

Instead of creating one flat network, the VNet can be divided into multiple subnets for different workloads.

---

## 2. Subnets Provide Network Segmentation

I created three subnets:

- `snet-web` – `10.0.1.0/24`
- `snet-app` – `10.0.2.0/24`
- `snet-data` – `10.0.3.0/24`

This helped me understand how different parts of an application can be separated logically while still existing inside the same Virtual Network.

The three subnets represented:

`Web → Application → Data`

---

## 3. NSGs Control Network Traffic

I learned how Network Security Groups act as traffic filters for Azure network resources.

NSGs contain inbound and outbound security rules that determine which traffic should be allowed or denied.

Each rule can evaluate information such as:

- Source
- Destination
- Port
- Protocol
- Action
- Priority

---

## 4. NSG Rule Priority Matters

Azure evaluates NSG rules according to their priority.

Lower numerical values have higher priority.

For example, a rule with priority `100` is evaluated before Azure's default rules with priorities such as `65000` and `65500`.

This showed me why rule planning is important when designing network security policies.

---

## 5. Security Can Be Applied Per Network Tier

Instead of using one NSG for the entire environment, I created:

- `nsg-web`
- `nsg-app`
- `nsg-data`

and associated each NSG with its corresponding subnet.

This allowed each tier to have security rules appropriate for its role.

---

## 6. Traffic Can Be Restricted by Source Network

For the application tier, I created a rule that allowed TCP port `8080` from:

`10.0.1.0/24`

This represented traffic coming from the web subnet.

For the data tier, I allowed TCP port `1433` from:

`10.0.2.0/24`

This represented SQL traffic coming from the application subnet.

This helped me understand that network rules can be much more specific than simply allowing a port from any source.

---

## 7. Three-Tier Architecture Improves Separation

The project helped me understand the networking concept behind a three-tier application:

### Web Tier
Handles web-facing traffic.

### Application Tier
Handles application/business logic.

### Data Tier
Represents the database layer.

Separating these tiers creates clearer network boundaries and allows different security controls to be applied to each layer.

---

## 8. Default and Custom NSG Rules Work Together

Azure automatically creates default NSG rules.

During the project, I added custom rules with higher priority to define the traffic required by the lab.

I learned that custom rules do not replace the default rules automatically. Instead, Azure evaluates the applicable rules according to priority.

---

## 9. Azure Resource Visualizer Helps Validate Architecture

Using Resource Visualizer helped me see the relationships between the Virtual Network and Network Security Groups.

This was useful for validating that the resources were connected as expected rather than relying only on individual configuration pages.

---

## 10. Resource Cleanup Is Part of Cloud Administration

After completing the project and capturing the required evidence, I deleted the project's resource group.

Because the networking resources were contained within the resource group, deleting it provided a convenient way to clean up the lab environment.

This reinforced an important cloud practice:

> Create resources when required, validate and document the work, and remove resources that are no longer needed.

This helps maintain an organized Azure environment and reduces the risk of unnecessary cloud costs.

---

# What I Can Explain After This Project

After completing this lab, I can explain:

- What an Azure Virtual Network is
- Why subnets are used
- How CIDR ranges define subnet address spaces
- What Network Security Groups do
- How inbound NSG rules work
- How NSG priorities affect rule processing
- How an NSG can be associated with a subnet
- How source networks can be used to restrict traffic
- Why web, application, and data tiers can be separated
- How ports 80, 443, 8080, and 1433 were used in this lab
- How Azure Resource Visualizer can help inspect resource relationships
- Why cloud resources should be cleaned up after temporary labs