## Prerequisite
1. Oracle cloud account
2. Oracle database


## Oracle Cloud Architecture

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/01.png)



## Architecture component commentary

1. Region and Availability Domains

Oracle Cloud Infrastructure is hosted in regions and availability domains. A region is a localized geographic area, and an availability domain is one or more data centers located within a region. A region is composed of several availability domains. Most Oracle Cloud Infrastructure resources are either region-specific, such as a virtual cloud network, or availability domain-specific, such as a compute instance.

Availability domains are isolated from each other, fault tolerant, and very unlikely to fail simultaneously. Because availability domains do not share infrastructure such as power or cooling, or the internal availability domain network, a failure at one availability domain is unlikely to impact the availability of the others.

All the availability domains in a region are connected to each other by a low latency, high bandwidth network, which makes it possible for you to provide high-availability connectivity to the Internet and customer premises, and to build replicated systems in multiple availability domains for both high-availability and disaster recovery.

2. VCNs and Subnets

A cloud network is a software-defined network that you set up in Oracle data centers. A subnet is a subdivision of a cloud network.

Each subnet in a VCN exists in a single availability domain and consists of a contiguous range of IP addresses that do not overlap with other subnets in the cloud network.

3. Dynamic Routing Gateways (DRGs)

You can think of a DRG as a virtual router that provides a path for private traffic (that is, traffic that uses private IPv4 addresses) between your VCN and networks outside the VCN's region.

4. Compute (VM)

Oracle Cloud Infrastructure Compute lets you provision and manage compute hosts, known as instances . You can launch instances as needed to meet your compute and application requirements. After you launch an instance, you can access it securely from your computer, restart it, attach and detach volumes, and terminate it when you're done with it. Any changes made to the instance's local drives are lost when you terminate it. Any saved changes to volumes attached to the instance are retained.


5. Autonomous Data Warehouse (ADW)

Autonomous Data Warehouse Cloud provides an easy-to-use, fully autonomous data warehouse that scales elastically, delivers fast query performance and requires no database administration. It is designed to support all standard SQL and business intelligence (BI) tools, and provides all of the performance of the market-leading Oracle Database in an environment that is tuned and optimized for data warehouse workloads.
