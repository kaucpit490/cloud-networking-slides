---
# try also 'default' to start simple
theme: default
presenter: true
title: 'Cloud Computing | Networking'
titleTemplate: '%s - CPIT-490'
# apply any windi css classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: | 
    Cloud Computing | Compute and Storage
# page transition
transition: slide-left
# use UnoCSS
css: unocss
# Make content selectable/copyable
selectable: true
favicon: '/images/favicon.ico'
# Make slides downloadable as PDF
download: true
exportFilename: cloud-computing-slides
export:
  format: pdf
  timeout: 30000
  dark: false
  withClicks: false
  withToc: false
# enable slide recording and drawing
record: build
drawings:
  enabled: true
  persist: false
  presenterOnly: false
  syncAll: true
---

# Cloud Computing Networking

### CPIT-490/632: Cloud Computing Architecture


<div class="absolute left-30px bottom-30px">
Spring 2024 &copy; Khalid Alharbi, Ph.D.
</div>


---
hideInToc: true
---

# Table of Contents

<Toc columns="2" maxDepth="1" mode="all" class="toc-list"/>

---

# Table of Contents
- Introduction to networking on the cloud
- Networking Fundamentals
- Virtual Private Cloud (VPC)
- IP addressing and network interfaces
- Subnets and routing
- Security Groups (SG) and Network Access List (NACL)
- NAT Gateways
- Reverse Proxy
- Load Balancing
- VPNs
- DNS
- Content Delivery Network (CDN)
- Web Application Firewall (WAF)
- Hands-On Lab: setting up a cloud network



---

# Introduction

- Similar to traditional on-premises networking, cloud networking provides the ability to build, administer, operate, and securely connect networks across all on-premise network, cloud, and edge locations.
- In a traditional on-premise data center, all compute resources such as VMs are interconnected using dedicated hardware devices (i.e., routers and switches) to control network traffic.
- In the cloud, all compute resources (e.g., VMs, containers, services provided as PaaS or FaaS) are interconnected using software-based controllers or interfaces through an approach known as SDN.
- **Software-defined networking (SDN)** is an approach to network management that uses software-based controllers or APIs to direct traffic on the network and communicate with the underlying hardware infrastructure.

---

## Introduction (Cont.)

<br>

- In a cloud environment, the physical hardware like routers and switches still exist, but they are abstracted away from the user's view.
- Cloud providers manage the physical networking devices.
- Customers of cloud providers interact with a software-defined  network (SDN) that is managed via APIs or a console, rather than having to configure physical network devices directly.
- SDN abstracts the underlying hardware infrastructure, allowing network traffic to be managed by a software-based, centralized server.
- Cloud providers utilize SDN to create virtual networks and move data easily across distributed locations.



---
layout: center
---

# Part 1: Networking Fundamentals



> Before we talk about cloud networking and server pools, we need to understand key terms in networking. Although these terms may differ across cloud providers, we'll adhere to the terminology used by the three major cloud providers: AWS, Azure, and GCP.


---


## IP Addressing: IPv4 and IPv6

- An IP address is a unique identifier assigned to a device connected to a network that uses TCP/IP for communication
- There are two versions of IP addresses: IPv4 and IPv6

| Feature | IPv4 | IPv6 |
|---------|------|------|
| Addressing Method | Decimal Notation (e.g., `192.168.0.1`) | Hexadecimal Notation (e.g., `2001:0db8:85a3:0000:0000:8a2e:0370:7334`) |
| Address Size | 32 bits | 128 bits |
| Total Possible Addresses | Approximately 4.3 billion | Approximately 340 undecillion (10^36) |
| Configuration | Manual or DHCP | Auto-configuration (SLAAC) or DHCPv6 |

---

## IP Addressing: Private, Public, Dynamic, and Static IPs

- A **Private IP** is an IP address that is not directly accessible from the internet. [RFC 1918 &#8599;](https://datatracker.ietf.org/doc/html/rfc1918) specifies the ranges of IP addresses that are reserved for private networks:
  - `10.0.0.0` to `10.255.255.255` (10.0.0.0/8)
  - `172.16.0.0` to `172.31.255.255` (172.16.0.0/12)
  - `192.168.0.0` to `192.168.255.255` (192.168.0.0/16)

- A **Public IP** is an IP address that is accessible from the internet. Most cloud providers recycle public IP addresses when are no longer used. For example, if you restart a VM, it will get a new public IP address.
- A **Dynamic/Ephemeral IP** is an IP address that is assigned to a device for a limited period of time and is constantly changing as devices connect and disconnect from the network. It's often assigned by a DHCP server (Dynamic Host Configuration Protocol).
- A **Static IP** is an IP address that is reserved for the customer's use and is accessible from the internet. It remains the same over time even after shutting down a VM. It's often assigned manually.


---

# IPv4 Addresses pricing
- Public IPv4 addresses are an increasingly scarce resource
- Most cloud providers apply hourly charge for public IPv4 addresses whether attached to a service or not
- Public IPv4 prefixes are charged per IPv4 per hour
  - AWS & GCP: $0.005/hour = $3.6/month
  - Azure: $0.004/hour = $2.88/month
- Reserved static IPv4 addresses that are not attached to a resource incur additional charges
  - Utilize them or free them up for use by another customer
- IPv4 charges are intended to accelerate the adoption of IPv6
- IPv6 usage is growing and becoming the default in many new network configurations
- As of April 2024, IPv6 addresses are free on most major cloud providers.


---

## Network Interface

- A *Network Interface* is also known as Network Interface Controller or Network Interface Card or Network Adapter.
- In a traditional data center, it is often a dedicated hardware component that connects a device to a  network.
- In a cloud environment, a network interface is a logical networking component in a VNet that represents a virtual network card that can be attached or detached from VM instances as needed.
- Network interface may be configured to include the following:
  - A primary private IPv4 address from the IPv4 address range of the VNet.
  - A primary IPv6 address from the IPv6 address range of the VNet.
  - One or more secondary private IPv4 address from the IPv4 address range of the VNet.
  - One public IPv4 address and one or more IPv6 addresses
  - One or more security groups
  - A MAC address, a description and a source/destination check flag

---

# Virtual Network (VNet)
- A logically isolated virtual network within the cloud platform's multi-tenant environment.
- It is dedicated to a customer of a cloud provider.
- It is a virtual version of a physical network that is logically isolated in a region for provisioning resources.
- VNets are region-scoped (i.e. created in a specific region in the cloud provider's infrastructure). 
- A region is a geographical area where the cloud provider has data centers.
- A VNet spans all of the Availability Zones (AZs) in a region.
  - An AZ is an isolated location within a region equipped with its own power supply and network infrastructure
- A single VNet cannot span multiple regions.
- Multiple VNets can be connected using a technology called VNet peering.
- VNets provide networking functionality equivalent to a traditional physical network, including IP addressing, IP range allocation, subnet creation, route table configuration, network gateway attachment, and security settings management.

---

## Virtual Network (VNet) (Cont.)

- A virtual network is comprised of one or more partitions called **subnets**.
- After you create a VNet, you can add one or more subnets in each Availability Zone (AZ).
  - An AZ is an isolated location within a region equipped with its own power supply and network infrastructure.
- Most cloud providers, including AWS, GCP, and Azure, do not charge for creating and using a Virtual Private Cloud (VNet).
- They typically only charge for the resources and services that run within the VNet such as VMs, compute resources, storage resources, and data transfer.
- They also charge for additional VNet capabilities like VPC peering, encryption, VPN connections, NAT gateways, or dedicated IPs.
- Pricing is always subject to variation and change, so it's recommended to check the pricing details of your cloud provider.
- Virtual networks are called Virtual Private Cloud (VPC) by AWS and GCP, while Azure refers to it as a Virtual Network (VNet)


---
layout: center
---

## Virtual Network (VNet)
<br>

<img alt="VNet spanning across three zones" src="/images/vpc.svg" style="width: 60%;" />

---
layout: center
---

## Virtual Network (VNet) with subnets
<br>

<img alt="VNet spanning across three zones with three subnets" src="/images/vpc-subnet.svg" style="width: 80%;" />

---

## VNet/VPC Address Space
- IP addresses enable communication between resources within your VPC, as well as with resources on the internet.
- When you create a a virtual network, you specify its IP addresses in one of the following:
  - **IPv4 only**: The VNet/VPC has an IPv4 CIDR block but does not have an IPv6 CIDR block.
  - **IPv6 only**: The VNet/VPC has an IPv6 CIDR block but does not have an IPv4 CIDR block.
  - **Dual-stack mode**: The VNet/VPC has both an IPv4 CIDR block and an IPv6 CIDR block. Resources can communicate over IPv4, or IPv6, or both
- The IP addresses for the VNet/VPC are represented using Classless Inter-Domain Routing (CIDR) notation.

---

## IPv4 VNet/VPC Address Space

<style>
thead {
  background-color: #f2f2f2;
}
</style>

- When creating a VNet/VPC, it is required to define an IPv4 CIDR block for it.
- The CIDR block is created from the private IPv4 address ranges as specified in [RFC 1918 &#8599;](https://datatracker.ietf.org/doc/html/rfc1918).
- A route table is created for each VNet/VPC
- The route table contains a set of rules that determine where network traffic from the VNet/VPC is directed.

- IPv4 CIDR blocks:

  | RFC 1918 Range   | Example CIDR Block | Number of IP Addresses |
  |------------------|--------------------|------------------------------------|
  | 10.0.0.0 - 10.255.255.255   | 10.0.0.0/16   | 2<sup>16</sup>=65,536  |
  | 172.16.0.0 - 172.31.255.255 | 172.16.0.0/12 | 2<sup>20</sup>=1,048,576 |
  | 192.168.0.0 - 192.168.255.255 | 192.168.0.0/16 | 2<sup>16</sup>=65,536 |



---

## Subnets

- A subnet is a logical subdivision of an IP network that can be private or public.
- Each subnet must be created in a single Availability Zone and cannot span zones.
- The IP address range of the subnet is defined depending on the configuration of the VNet/VPC IP address range.
- The subnet will draw from the IP address range of the VNet/VPC, which could be IPv4 only, IPv6 only, or a Dual stack of both.
- Each subnet must be associated with a route table
- Subnet type depends on routing table configuration:
  - **Public subnet**: Has a direct route rule to an internet gateway.
  - **Private subnet**: Has no direct route rule to an internet gateway.
  - **VPN-only subnet**: Has a route rule to a Site-to-Site VPN connection.

---

## Network Security Groups
- Network security refers to the practice of safeguarding resources from unauthorized access or attacks
- Securing the resources in the subnet is critical to prevent unauthorized access and mitigate threats
- All systems that are not client-facing should be placed in private subnets
- Internet access to resources located in private subnets should be done through a NAT gateway or Bastion Host (for SSH)
- Security groups control the inbound and outbound traffic for  resources within both VNet and subnet
- We can use security groups to allow or disallow inbound or outbound traffic
- Security groups can be declared for both inbound and outbound traffic
- Network interfaces associated with resources such as VM instances inherit the rules defined in the security groups they are associated with.


---

## Routing and Peering
- Without routing, network devices would be unable to route traffic to its destination and facilitate communication between subnets or networks.
- Routing is the process of directing network traffic from one place to another through routing tables
- Routing tables exist at the VNet/VPC level as well as the subnet level.
- **Network Route Tables**: are a set of rules that are used to determine where network traffic from subnets and/or gateways are directed.
- **Network Peering**: is a networking connection between two VNets/VPCs that enables the routing traffic between them using private IP addresses.


---


## Configuring Route Tables 

<style>
  table{
    font-size: 0.7em;
  }
</style>

- In a cloud environment, there are two types of route tables: **default or main route table** and **custom route table**.
  - The default or main route table contains the default route rules that each VPC/VNet has.
  - The custom route table contains the route rules for specific subnets.
- Route tables use CIDR block notation
  - For IPv4, the CIDR notation `0.0.0.0/0` represents all possible IPv4 addresses
  - For IPv6, the CIDR notation `::/0` for represents all possible IPv6 addresses

---

## Configuring Route Tables (Cont.)

- A route table may include the following components:

  | Component | Description |
  |------|-------------|
  | Destination | The IP address range (CIDR block) to which a route applies. |
  | Target/Next Hop | The next destination for network traffic if the destination matches. |
  | Association | The relationship between a subnet and a route table. |
  | Routes | Rules in the route table that dictate the flow of network traffic. |
  | Route Propagation | The automatic addition of routes to a route table from a virtual private gateway. |

---

## Example of a the Main VPC Route Table in AWS

| Destination | Target | Status | Propagated |
|-------------|--------|--------|------------|
| 10.0.0.0/16 | local  | Active | No         |
| 0.0.0.0/0   | igw-id | Active | No         |


- The first row is the local route, which enables communication within the VPC, and it cannot be modified.
- The second row is a custom route that directs all other traffic (0.0.0.0/0) to an Internet Gateway (igw-id), allowing instances in the VPC to access the internet.


---

## Example of a Subnet Route Table in Azure

| Name | Address Prefix | Next Hop Type | Next Hop Address |
|------|----------------|---------------|------------------|
| Route1 | 10.0.1.0/24 | VNetLocal | - |
| Route2 | 0.0.0.0/0 | Internet | - |

- The first row, Route1, is a route that directs all traffic destined for the 10.0.1.0/24 address prefix within the virtual network.
- The second row, Route2, is a route that directs all other traffic (0.0.0.0/0) to the internet.

---

## Example of a Subnet Route Table in GCP

| Name | Destination | Next Hop | Priority |
|------|-------------|----------|----------|
| Route1 | 10.0.1.0/24 | 10.0.1.4 | 1000 |
| Route2 | 0.0.0.0/0 | default-internet-gateway | 1000 |

- The first row, Route1, is a route that directs all traffic destined for the 10.0.1.0/24 address prefix to a next hop at 10.0.1.4 within the virtual network.
- The second row, Route2, is a route that directs all other traffic (0.0.0.0/0) to the default internet gateway.

---

## Network Interface

- A Network Interface is a logical networking component in a VNet that represents a virtual network card.
- It enables a VM instance to communicate with the Internet, VNet/VPC, and on-premise resources.
- It may be associated with a private IPv4 address, public IP address, a MAC address, and one or more IPv6 addresses.
- It can be attached to a VM or detached from a VM.
- Network interfaces are placed in a subnet, which is placed in an AZ within the VNet/VPC, which is placed in a region.
- A VM instance has one primary network interfaces and may have additional network interfaces.
- Security groups are attached to network interfaces, which may be attached to VM instances


---

## Internet Gateway

- An Internet Gateway is a networking component that allows communication between a VPC and the internet.
- An Internet gateway is a VNet/VPC component, so it is not associated with a specific subnet
- It is a managed networking component that is horizontally scaled, redundant, and highly available
- It's necessary for instances in a VNet/VPC that have a public IP address to send and receive traffic from the internet.

---
layout: center
---

## How to get Internet Access

- To enable VM instances to receive and send traffic from the internet, we can do so in one of the following methods:

  - Using an Internet Gateway by assigning a public IP to the instance
  - Using a NAT device (without assigning a public IP to the instance)

---

## How to get Internet Access
<br>

### Method 1: Assigning a public IP to the instance
<br>

- Create a **VNet/VPC** in a region
- Create a **subnet** within the VNet/VPC
- Create an **Internet Gateway** and attach it to the **VNet/VPC**
- Create a **route table** for the subnet and add a route with a destination of `0.0.0.0/0` for IPv4 traffic or `::/0` for IPv6 traffic, and a target/nextHop of the Internet gateway unique ID
- Add **security groups** to the subnet to allow the desired Internet traffic to flow to and from the instance
- Create a **VM instance** and place it in the subnet
- Assign a **public IPv4** address or an **IPv6 address** for the instance

---

## How to get Internet Access
<br>

### Method 2: Without assigning a public IP to the instance
<br>

- Create a **NAT Gateway** or **NAT instance** and attach it to the **VNet/VPC**.
- Create a **subnet** within the VNet/VPC.
- Create a **route table** and add a route with a destination of `0.0.0.0/0` for IPv4 traffic or `::/0` for IPv6 traffic, and a target/nextHop of the NAT Gateway or NAT instance unique ID.
- Add **security groups** to the subnet to allow the desired Internet traffic to flow to and from the instance.
- Create a **VM instance** and place it in the subnet.
- The instance will be able to access the internet but won't be directly reachable from the internet.

---

## NAT Gateway
- A NAT service that enables network traffic to flow in and out of a virtual network (VPC).
- It's primarily used to enable outbound Internet access for resources in a private subnet.
- It allows instances in a private subnet to connect to the Internet or services outside the VPC, but prevents and blocks incoming Internet connection from initiating a connection with those instances.
- It is useful when instances in a private subnet need to reach the public Internet for software updates but do not want to be publicly accessible.
- A NAT gateway can be public or private:
  - A **public NAT gateway** enables instances in private subnets to connect to the internet through a public NAT gateway, but cannot accept unsolicited inbound connections from the internet.
  - A **private NAT gateway** enables instances in private subnets to connect to other VPCs or your on-premises network through a private NAT gateway through a transit gateway or a virtual private gateway.

---

## NAT Gateway Pricing
- A NAT gateway is an Availability Zone scoped component, so it can only serve instances in the same AZ.
- Most cloud providers charge an hourly basis for the duration that a NAT gateway is provisioned and in use.
  - AWS and Azure: $0.045/hour ~ $32 per month
  - GCP charges based on the number of VM instances that are using the NAT gateway.
- Additional costs are also applied for the data processed by the NAT gateway, typically for outbound data, also known as **egress** traffic.
- Generally, there are no charges for inbound data, or **ingress** traffic, which is data entering the network from the internet.

---

## Web Application Firewall (WAF)
- A reverse-proxy that forces clients to pass through the WAF before reaching the server.
- Helps mitigate many common attacks and vulnerabilities.
- Creates a shield between a web app and the public Internet
- It can be also used to block requests from a specific country or geolocation.
- Examples: ModSecurity, AWS WAF, Azure WAF, GCP Cloud Armor, and Cloudflare WAF.

---

## Single-server Design
- We can't run a highly available website or an app on a single server.
  - It is a single point of failure (SPOF).
  - We can't update our application, host OS, or configuration without downtime.
  - It is vulnerable to load spikes and attacks (e.g., DoS).
  - It will eventually become crippled when there's an overflow of requests.


---

# Designing a Virtual Network (VNet/VPC)
- We will take an example of creating a virtual network that contains three subnets:
  - **Subnet for Load Balancers**. Servers that run HAProxy.
  - **Subnet for Web Servers**.
    Database tier: At this level, the solution includes the servers that are responsible for running the database.




---

# Load Balancing (I)

- A load balancer is a form of proxy server that distributes incoming network traffic across a group of downstream web servers (commonly known as server pool or server farm).
-  The server pool can be VM instances, containers, scale set, serverless functions, or IP addresses
- A load balancer also monitors the health and status of the server pool to ensure availability and timely response.

---


# Load Balancing (II)
![Load Balancer Diagram](/images/load-balancer-diagram-wh.png)

---

## Load Balancing (III)
- A load balancer (LB) does not process incoming requests but routes them to other systems.
- If a server is not responding, it will detect this problem, stop directing traffic to that server, and remove it from the server pool.
  - This is done through the "health-check" mechanism.

---

## Load Balancing (IV)
- Load balancers can be hardware-based or software-based.

---

## Hardware Load Balancers
- A hardware LB uses a proprietary device/appliance and a specialized operating system to distribute traffic across multiple servers.
  - can be scaled by purchasing bigger appliances from the vendor.
  - over-provisioned to handle larger requests.
  - more expensive and less flexible.

---

## Software Load Balancers
- A software LB uses Software solutions that run on commodity hardware.
  - can be installed on almost any VM or computer architecture.
  - scale elastically to meet demand.
  - less expensive and more flexible.

---

## Load Balancers Availability
- A load balancer itself can become a single point of failure (SPOF) unless you:
  - Have two LBs, one serving traffic and one running as a passive backup.
  - Have two LBs, serving traffic simultaneously.
  - Use a fully-managed load balancer that automatically distributes your incoming traffic across multiple targets, with an SLA of at least 99.99%.

---

## Benefits of Load Balancing
- High Availability
- Scalability
- Redundancy
- Flexibility
- Efficiency
- Security

---

### Commonly Used Load Balancing Algorithms

- **Round Robin**
  - Requests are distributed across a group of servers sequentially in a fixed order.
- **Load equalization/Least Time**
  - New requests go to the server that's handling the least requests or connections.
- **Partitioning/IP Hash**
  - The hash of the client's IP determines which server handles the request.

---

## Types of Load Balancers (I)
![](/images/load-balancer-network-osi-wh.svg)

---

## Types of Load Balancers (II)

A load balancer may function at the 7th, 4th, or 3rd layer the Open Systems Interconnection (OSI) model:

- Layer-7 Load Balancer: Application Load Balancer
- Layer-4 Load Balancer: Network Load Balancer
- Layer-3 Load Balancer: Gateway Load Balancer

---

### Application Load Balancer (ALB)
- Layer 7 load balancer that distributes incoming traffics across multiple targets.
- Targets can be VM instances, containers, IP addresses, or serverless functions.
- Support for HTTP header conditions and methods, URL path, query parameters, and source IP addresses.
- Protocol listeners: HTTP, HTTPS, and gRPC

---

## Application Load Balancer (ALB) (Cont.)
- ALB supports (SSL/TLS) termination at the ALB.
  - Traffic flows encrypted to the ALB and then flows unencrypted to the backend servers. 
  - This improves performance and free the servers from costly encryption and decryption overhead.
- If security compliance requires end to end SSL/TLS encryption, encrypted communication to the servers can be also configured at the ALB.

---

### Application Load Balancers (ALBs) for Linux
- The most common load balancers for Linux are:
 - [nginx](https://nginx.org/en/docs/http/load_balancing.html)
 - [HAProxy](http://www.haproxy.org/)

 [Apache httpd](https://httpd.apache.org/docs/2.4/mod/mod_proxy_balancer.html) also has a load-balancing module although it's not as widely used as nginx and HAProxy.

---

## Fully-managed Application Load Balancers (ALBs)
- AWS has a suite of load balancers called Elastic Load Balancer, which has an Application Load Balancer (ALB).
- GCP has a suite of load balancers called Google Cloud Load Balancing that has an HTTP Load Balancers.
- Azure's ALB is called Application Gateway.

---

## Application Load Balancer Features
- Secure Sockets Layer (SSL/TLS) termination
- Autoscaling
  - scale-up/scale-out or scale-in/scale-down based on changing traffic load patterns.
-  Multiple availability zone (AZ) redundancy.
- Static virtual IP address
- Web Application Firewall

---

## Application Load Balancer Features (Cont.)
- URL-based routing
  - requests for _http://example.com/video/*_ are routed to the _VideoServerPool_, and _http://example.com/images/*_ are routed to the _ImageServerPool_.

---

## Application Load Balancer Features (Cont.)
- Host-based routing
  - requests for _http://sa.example.com_ are routed to the _SAServerPool_, and _http://us.example.com_ are routed to the _USServerPool_.
  - requests for _http://example.sa_ are routed to the _SAServerPool_, and _http://example.us_ are routed to the _USServerPool_.

---

## Application Load Balancer Features (Cont.)
- More routing features
  - redirect HTTP requests to HTTPS
  - redirect from and to any port
  - redirect to an external site
- Sticky sessions
  - bind a user's session to a specific target
- Authenticate users
  - securely authenticate the client sending requests.

---

## Application Load Balancer Features (Cont.)
- Connection Draining
  - give all in-progress requests time to complete when a VM is removed from the pool or server updates.
- Custom error pages
   - 403 web application firewall error when WAF detects malicious traffic and blocks it
   - 502 maintenance page when there's no backend server to route to.

---

## Types of Load Balancers (II)

- <span style="color:#666">Layer-7 Load Balancer: Application Load Balancer</span>
- Layer-4 Load Balancer: Network Load Balancer
- Layer-3 Load Balancer: Gateway Load Balancer

---

### Network Load Balancer (NLB)
- Layer 4 load balancer that distributes incoming traffics across multiple targets based on the source and destination IP addresses and ports available in the packet header, without considering the contents of the packets.
- Targets can be VM instances, containers, or IP addresses.
- Supports ultra high performance with million of requests per second, low latency, and TLS offloading at scale.

---

### Network Load Balancer (NLB) (cont.)
- Protocol listeners: TCP, UDP, and TLS
- After a TCP network load balancer receives a connection request, it selects a target and attempts to open a TCP connection to the selected target on the port specified in the listener configuration.
- Examples: AWS Network Load Balancer and Azure Load Balancer.

---

## Types of Load Balancers (II)

- <span style="color:#666">Layer-7 Load Balancer: Application Load Balancer</span>
- <span style="color:#666">Layer-4 Load Balancer: Network Load Balancer</span>
- Layer-3 Load Balancer: Gateway Load Balancer

---

### Gateway Load Balancer (GLB)
- Layer 3 load balancer
- used for managing and scaling virtual appliances such as firewalls and intrusion detection systems.
- Protocol listeners: IP
- Targets can be IP or VM instances.
- It is deployed in the same VPC as that of the virtual appliances.

---

### Gateway Load Balancer (GLB) (Cont.)
- It listens for all IP packets across all ports and forwards traffic to the target group that's specified in the listener rule.
- It provides private connectivity between virtual appliances in the service provider VPC, and application servers in the service consumer VPC.
- Examples: AWS is the only provider that has a layer-3 LB called Gateway Load Balancer.

---

### Summary of Load Balancer Types
- **Layer-3 LB** takes routing decisions based on <u>IP addresses</u> alone (source & destination).
- **Layer-4 LB** takes routing decisions based on <u>IP addresses</u> and <u>TCP or UDP ports</u>.
- **Layer-7 LB** takes routing decisions based on <u>IP addresses</u> and <u>TCP or UDP ports</u>, and the <u>content of the request</u> (URL, HTTP headers or cookies).

---


## Application Load Balancer Example


- We will use a fictional [bakery API server](https://gitlab.com/cpit490/web-server) that has the following endpoints:

  - `/us/menu`: returns the US menu of the bakery store.
  - `/sa/menu`: returns the SA menu of the bakery store.
  - `/health/`: A heartbeat endpoint for API health check. This is useful for the LB to check if the web server is running or not.
  - `/fatal`: for terminating the web server. This is useful for testing auto-scaling.


![](/images/ALB-demo-example-wh.png)


## Steps
1. Create a VNet with four subnets.
2. Create a layer-7 load balancer inside a subnet. 
3. Create three backend pools.
4. Launch VMs in the remaining 3 subnets.
5. Add target VMs to backend pools.
6. Create a backend listener.
7. Configure the load balancer to use path-based routing rules to send incoming requests to the appropriate servers in the backend pools.

---

# Demo

<Youtube id="al1WZX4ouHU" width="100%" height="100%"  />


---

# Wrapping up

- We have covered the fundamental concepts of the neccessary knowledge to implement and manage network architecture:
  - Virtual Private Cloud networks (VPC/VNet)
  - Subnets and Routing
  - Network services
  - Hybrid and multi-cloud interconnectivity
  - Cloud Network Management
  - Load Balancing
  - Reverse Proxy
  - DNS
- If you wish to validate your knowledge of cloud network infrastructure and services, you may consider earning a Cloud Network Engineer certification offered by major cloud providers like [Microsoft](https://learn.microsoft.com/en-us/credentials/certifications/azure-network-engineer-associate/?practice-assessment-type=certification), [Google](https://cloud.google.com/learn/certification), and [Amazon](https://aws.amazon.com/certification/).

---

# References
- [AWS "AWS Well-Architected". Retrieved 05 January 2024](https://docs.aws.amazon.com/wellarchitected/)
- [Fowler, Martin (10 September 2018). "Serverless Architectures". Retrieved 26 January 2018.](https://martinfowler.com/articles/serverless.html)