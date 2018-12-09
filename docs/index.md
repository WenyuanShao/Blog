<div align=center># **SDN and NFV**</div>

## What is SDN (software defined network)?
Wikipedia define SDN as "a technology for cloud computing that facilitates network management and enables programmatically efficient network configuration in order to improve network performance and monitoring". Or I will put it this way, SDN is a technology to separate the network control plane from the forwarding plane (or routing plane). SDN intended to make the whole network open and programmable.

## SDN Model
SDN model is quiet similar as operating system model which could be more familiar to us. As shown in graph_1, operating system model mainly has three layers: low level hardware layer, operating system layer and high application layer. 

<div align=center>![graph_1](/src/graph_1.png "graph_1")</div>

While in SDN model, Instead of having operating system, SDN model has a **Network Operating System** layer which also could be named as **SDN controller** layer. Moreover, as SDN focus on "How to process a packet", It should be connected to different forwarding devices. 

<div align=center>![graph_2](/src/graph_2.png "graph_2")</div>