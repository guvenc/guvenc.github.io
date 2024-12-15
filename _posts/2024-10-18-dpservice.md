---
layout: post
title: "Accelerating Network Performance with dpservice: An In-Depth Look"
date: 2024-10-18
categories: [Software Engineering]
---

#### Summary:
Dataplane Service, in short [dpservice](https://github.com/ironcore-dev/dpservice) is a [DPDK](https://www.dpdk.org/) based Software Router to divide your datacenter network into [VPCs](https://en.wikipedia.org/wiki/Virtual_private_cloud) (IPv4 and IPv6 overlays) easily with Kubernetes integration and accompanied Network Functions like Loadbalancer, NAT, Connection Tracking.

#### Accelerating Network Performance with dpservice: An In-Depth Look

In the rapidly evolving world of network infrastructure, the need for efficient, high-speed data processing is more critical than ever. dpservice, an open-source project, addresses this need by providing a robust data plane service leveraging the Data Plane Development Kit (DPDK). This blog post explores what dpservice is, its core features, and its potential impact on network performance.

##### What is dpservice?

dpservice is a DPDK-based data plane service designed to function as a fast L3 router and SDN (Software-Defined Networking) enabler. It can be deployed on compute nodes or [SmartNICs](https://en.wikipedia.org/wiki/Data_processing_unit) (Smart Network Interface Cards), providing flexible and scalable network solutions. It can create overlay networks and VPC like isolation, including several network functions. At its core, dpservice uses the DPDK Graph Framework, which allows for efficient packet processing and flow management.

##### Key Features of dpservice

1. **High-Speed Data Plane:**
   dpservice uses DPDK to achieve high-speed packet processing. It supports both offloaded and non-offloaded modes, allowing for dynamic handling of network traffic:

   - *Offloaded Mode:* The first packet of each flow is processed in software, and subsequent packets are offloaded to hardware for accelerated processing.
   - *Non-Offloaded Mode:* All traffic is handled in software using Poll Mode Drivers (PMDs) and dedicated CPU cores.
2. **SR-IOV Support:**
   Single Root I/O Virtualization ([SR-IOV](https://en.wikipedia.org/wiki/Single-root_input/output_virtualization)) is supported by dpservice, which allows multiple virtual machines to share a single physical network interface card (NIC). This enhances performance by reducing the overhead associated with virtualization.
3. **Advanced Networking Capabilities:**
   dpservice offers a wide range of networking features essential for modern network environments:

   * *L3 Routing with L2 Capabilities:* It functions primarily as a Layer 3 router with basic Layer 2 functionalities.
   * *IP in IPv6 Tunneling:* Supports tunneling for uplink traffic, enhancing compatibility and flexibility in network configurations.
   * *Overlay Networks:* dpservice can build overlay networks supporting both IPv4 and IPv6, enabling the creation of isolated Virtual Private Clouds (VPCs) for multi-tenant environments.
   * *Virtual Network Interfaces:* Through gRPC, users can add virtual network interfaces, load balancers, NAT gateways, and configure routes. Virtual Network Interfaces can be SRIOV-VF backed or TAP device backed.
4. **Horizontally Scalable NAT Gateways:**
   The NAT gateway functionality in dpservice is horizontally scalable, meaning each deployed dpservice can handle part of the NAT Gateway traffic and more dpservices instances can be added to handle more traffic. This scalability is crucial for managing large-scale network environments efficiently.
5. **Maglev Hashing for Loadbalancing:**
   dpservice employs [Maglev hashing](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/44824.pdf) for load balancing, a technique that ensures even, sticky and addition/deletion resistant distribution of traffic across multiple servers, enhancing performance and reliability.
6. **NAT64 for IPv6 Overlay:**
   dpservice supports [NAT64](https://en.wikipedia.org/wiki/NAT64), a mechanism that allows IPv6-only overlay clients to communicate with IPv4 servers, facilitating the transition to IPv6.
7. **Protocol Support:**
   The service supports essential networking protocols on its downlink ports such as DHCPv4, DHCPv6, Neighbor Discovery, and ARP, ensuring comprehensive network management and operation.
8. **Firewall:**
   Each created virtual interface on dpservice can also receive egress/ingress firewall rules via gRPC which are stateful and conntracked.
9. **Observability:**
   dpservice provides Prometheus metrics for monitoring and observability, allowing users to track network performance and troubleshoot issues effectively.

##### dpservice Design / Architecture

dpservice is a user space application which can be visualized together with SRIOV and Promethous based observability aspects as seen in the diagram below. It can be deployed on a compute node/hypervisor to bring virtual machines to overlay network and/or on a bare-metal server with SmartNIC to bring the bare-metal server to overlay network, in latter case dpservice needs to run directly on SmartNIC.

[![Overall Architecture](/assets/images/overall_dpservice.png)](/assets/images/overall_dpservice.png)


The dpservice utilizes a sophisticated graph-based dataplane using [DPDK Graph library](https://doc.dpdk.org/guides/prog_guide/graph_lib.html), which can be visualized in the detailed diagram as below:

[![Dataplane Architecture](/assets/images/20240602_183806_dataplane.png)](/assets/images/20240602_183806_dataplane.png)



This architecture facilitates the following:

* Soft Path Processing:
  In the soft path, all packets are processed in software, which allows for flexibility and the handling of complex routing rules and stateful services like NAT and load balancing.
* Traffic Offloading:
  Using DPDK's rte_flow API, dpservice can offload specific traffic flows to SmartNICs. This offloading reduces the load on the CPU and accelerates packet processing by utilizing hardware capabilities.
* Connection Tracking:
  dpservice includes connection tracking, which reduces the need for repeated table lookups in the dataplane. By keeping track of active connections, it can efficiently manage stateful services and maintain high throughput even when traffic remains in the soft path.

##### Deployment and Use Cases

Deploying dpservice can be done on a compute node/hypervisor to bring virtual machines to overlay network and/or on a bare-metal server with SmartNIC to bring the bare-metal server to overlay network, in latter case dpservice needs to run directly on SmartNIC. This flexibility makes it suitable for various use cases, including:

* Data Centers (on-premise):
  Enhancing the performance of data center networks by providing fast and efficient routing and network management.
* Enterprise Networks:
  Enabling advanced SDN features and improving network performance in large enterprise environments.

##### Conclusion

dpservice represents a significant advancement in data plane services, offering high-speed packet processing, advanced networking capabilities, and robust scalability. By leveraging DPDK, it provides a powerful solution for modern network demands, making it a valuable tool for data centers and large enterprises looking to enhance their network performance.

