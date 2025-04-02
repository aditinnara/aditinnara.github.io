---
layout: page
title: Distributed System Demo
description: a fault-tolerant distributed system configured to handle active and passive replication
img: assets/img/dist-sys/console_output.png
importance: 1
category: technical
related_publications: false  
---

One of my favorite courses I took at CMU was called 18-749: Building Reliable Distributed Systems. Our final project for the course was to create a fully functional, fault-tolerant distributed system with automatic recovery of replicas and passive/active configurations.

We created four modules that make up our system:

1. **The server.**

   First, the server must handle client requests -- it must establish connections with all clients that attempt to connect to it, send responses, and update its own state based on these requests. In order to handle faults, we implemented replication, where multiple servers do the same work in order to handle crash-faults of one or two replicas. For this to work, we implemented periodic checkpointing between the server and its peers. We also configured two modes of replication: active and passive.

   During active replication, all servers do the exact same state calculations based on client requests. On the client-side, duplicate responses must be discarded. With passive replication, there is one primary server which performs every calculation, and 'checkpoints' its backup servers with the information. This way, not every server needs to be doing duplicate work.

   If the server is not yet ready to change its state because it hasn't yet been checkpointed by its peers after being restarted, the server logs all client requests until it becomes ready.

2. **The Local Fault Detectors (LFDs)**

   The Fischer-Lynch Impossibility Theorem states that, in theory, it is impossible to know whether a server is down or just slow. In practicality, we can send periodic heartbeats to servers to check whether they are down by taking note of whether the server is able to send us a heartbeat acknowledgement back. This is the job of the LFDs, which consistently heartbeat the servers to check if any of the servers are down or faulty. If a server is found to be faulty, the LFDs notify the Global Fault Detector (GFD) to remove that server from its members list.

3. **The Global Fault Detector (GFD)**

   The GFD keeps a count of every server that is currently active and non-faulty. If it receives a message from the LFD about a faulty server, it removes that server from its membership list. This membership information is then sent to the Replication Manager (RM).

   The GFD must also periodically heartbeat with the LFD so that the LFDs can detect whether the GFD is down.

4. **The Replication Manager (RM)**

   The RM deals with re-election of primary replicas in passive replication mode by receiving updates from the GFD about which servers were added or deleted from the system's active members. It also automatically attempts to bring up faulty servers after they've gone down.

All the python code and shell scripts to set up the server can be found [here](https://github.com/aditinnara/FaultTolerantDistributedSystem)!
