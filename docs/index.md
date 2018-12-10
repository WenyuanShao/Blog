# SDN and NFV

## What is SDN (software defined network)?
Wikipedia define SDN as "a technology for cloud computing that facilitates network management and enables programmatically efficient network configuration in order to improve network performance and monitoring". Or I will put it this way, SDN is a technology to separate the network control plane from the forwarding plane (or routing plane). SDN intended to make the whole network open and programmable.

## SDN Model
SDN model is quiet similar as operating system model which could be more familiar to us. Operating system model mainly has three layers: low level hardware layer, operating system layer and high application layer. 

While in SDN model, Instead of having operating system, SDN model has a **Network Operating System** layer which also could be named as **SDN controller** layer. Moreover, as SDN focus on "How to process a packet", It should be connected to different forwarding devices. 

## How does SDN work?
What happened if a packet arrives at a SDN controlled forwarding device? It could be divided into several steps:
> 1. The device will first parse the packet header. Then it either already knows what to do with the packet or it will query the network operating system to determine what to do.
> 2. The SDN controller will be the one who determine what to do with this packet. 
Let be more specific. If a packet arrives at the device D1, The forwarding device D1 will first look into its cache to see if it already knows how to deal with the packet. If not, it will talk to SDN controller and let the SDN controller make the decision. Once received the instructions, the forwarding device will take the assigned action on that packet whether that's modifying packet headers, drop the packet, send the packet to another device and so no. Also, the forwarding device will also caching the instructions so that in future packets don't acquire sending notifications to the SDN controller.

## Why SDN is better?
As SDN controller have global view of all of the forwarding devices and determine the actions to take on the packets, it will hide lots of complexity to the high level applications which may implement Network polices. For example, some high level application like firewall usually doesn't care about the details of how all of the forwarding devices are connected and possible paths between two points in its network. In this case, the SDN controller could provide some kinds of abstraction that, from the firewall's prospect of view, all of the forwarding devices in the network are connected to one single large switch.

## Details of components in the SDN model
> 1. Forwarding devices: in the SDN model, the forwarding devices are usual traditional hardware switches or software switches. However, these switches need to support some kind of programmable interfaces for example Openflow. Hardware switches usual have a better performance while software switches usual be more flexible.
> 2. There should be some communicating protocols between forwarding devices and SDN controller. The transferring messages include package handling instruction, packet arriving notifications, other notifications which indicate the state the of node or link and statistic informations like flow counters. 
> 3. SDN controller could provide multiple services such as topology services, inventory services, statistics services and host tracking services. 
>> + Topology services determines how these forwarding devices are connected to each other to build a sou called **topology graph**.
>> + Inventory services are used to track all SDN enable devices and to record information about them.
>> + Statistic services basically are reading counter information of a device like traffic counters and flow tables.
>> + Host tracker could tell the IP and MAC address of the hosts around the network by intercepting the packet headers. 
> 3. Application interfaces which provide interfaces for the network application to hock in to the whole SDN model.

## What is the benefit of SDN?
SDN or the software defined network makes it possible to treat the whole network as some kind of elastic resources. Also SDN makes it easier to handling different workload by creating a "subnet" or in other words, slicing up the network different. This could happen in different layers, for example, different types of traffics can be forward to a totally different SDN controller. While we can also use different network applications to handle different type of traffic. In this case, the network is more like a resource which could be shared by multiple applications.

## How is the scalability of SDN network?
Well, I'll say SDN provides fairly good scalability while there still could be limitation of scalability. There are several ways we can scale up our SDN. The first approach is clustering. Clustering is not a new idea. The idea of clustering is have a cluster of systems instead of one. Clustering also provides some kind of fault tolerance since if some of the systems go down, we still have fair amount of active functional systems. Another way to scale up the SDN is to have multiple regions and each regions has a separate SDN controller. Different regions can communicate information between each other use some protocols. Moreover, SDN systems could be designed in hierarchy which means there are different level of SDN controllers provides different levels of abstractions.

## What is NFV and why we use NFV?
Network function visualization offers a new way to deploy and manage network services and functions. NFV decouples the network functions (NFs) from hardwares so that we can run these services in virtual machine. Specific hardware which used to handler the packet flows are very fast but very expensive and has very less flexibility. Using NFV and running NFs in virtual machines will result in more flexibility. Since all NFs are running in VM, so it will provide more isolation and easier for us to manage. Though using NFV may have a bit low performance, the advantages are still considerable.

## How to use NFs to process packets?
Using NFs to process packets are quiet different form the traditional Linux packet processing. Traditional networking package processing could be divided into 4 steps. 
> 1. NIC uses DMA to get the data into a ring buffer in the kernel.
> 2. Interrupt will be trigged after the package arrives.
> 3. The package should be copied form kernel to user level.
> 4. User level should use a system call to send data.
The traditional packet process has a really bad performance since there are several copies of the packets and we have to use a system call to send data. To avoid multiple data movements and system calls we are using another approach which is user space packet process. The steps of user space packet process willbe as following:
> 1. NIC use DMA to copy data into user space buffer.
> 2. Instead of using interrupt, we use polling to get the packet.  
> 3. Use functions supported by the NICs to send data directly from user space.
There is a library call DPDK which is a high performance I/O library could be used to get and send data from user space.

## How to eliminate / hide overheads in packet processing
There are aspects in which DPDK or NFV could eliminate the overheads of packet processing.
> 1. Interrupt on x86 is very slow and it will cause context switch. So instead of using interrupt we use polling (a process where client will rely on some kind of external device to check its state).
> 2. kernel & user overheads are very high. As a result, instead of using kernel to communicate with the NIC, we can use some kind of user mode drivers to directly talk to hardware.
> 3. We can avoid thread scheduling overhead by ping each thread to a dedicated core.
> 4. To  reduce the paging overhead, we create huge pages which will reduce time of finding a packet because all page entries could be put in the TLB.
> 5. Since synchronization between threads are expensive, we use a producer (NIC) / consumer (NFs) model and use lock-free communication such as ring-buffer to pass data.
> 6. Finally we use batching to reduce the overhead of message passing.