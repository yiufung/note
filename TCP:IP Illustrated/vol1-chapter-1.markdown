# 1. Introduction

## 1.0 Concepts

**protocol**:

>  When a set of common behaviors is used with a common language, a
>  **protocol** is used. 

**protocol suite**: a collection of related protocols

**architecture** (or **reference model**): design that specifies how
various protocols of a protocol suite relate to each other and divide
up tasks to be accomplished. 

**TCP/IP**: a suite that implements Internet architecture, origins
from *ARPANET Reference Model*

## 1.1 Architectual Principles
* difference between Internet and WWW.

    * **Internet**: ability to provide basic communication of messages
between computers. 

    * **WWW(World Wide Web)**: an application that uses the Internet for
communication. 

* Primary goal:
> be able to interconnect multiple distinct networks and that multiple
> activities should be able to run simultaneously on the resulting
> interconnected network. 

### 1.1.1 Packets, Connections, and Datagrams

*  Origins from telephone network:
    *  **connection**: a physical circuit made for a duration of time
    *  provides **bandwidth** (or **capacity**).
    *  **latency**: upper bound on time. Gives predicatable service. 

*  **Packet Switching**:
    *  chunks from different sources be mixed together and pull
 apart later, called **multiplexing**. 
    *  path subject to change. 
    *  Advantages: resilient (free from worry of phyical attack) and
 better utilization of network links because of **statistical
 multiplexing**

*  Packets switch receives packets: 
    *  need to *schedule* the packets
    *  stored in *buffer memory* (or *queue*), processed in FCFS (or FIFO)
      1.  **Statistical Multiplexing**:
          *  FIFO + on-demand scheduling.
          *  Pro: High utilization, as busy as it can be. 
          *  Con: Limited predictability.
      2.  **Time-Division Multiplexing**:
      3.  **Static Multiplexing**:
          *  reserve certain amount of time or other resources for each connection
          *  Pro: Predictability
          *  Con: Poor utilization

*  **Connection-Oriented** networks:
    *  Build on cirtcuits or packets. 
    *  Virtual Circuits (VC) can be implemented atop connection oriented packets. Basis for X.25 protocol. 
    *  *state* be stored in each switch for each connection
    *  each packet carries small overhead info about the state
        * in X.25, 12-bit *logical channel identifier* (LCI, or *logical channel number*)
    *  at each switch, use LCI and *per-flow state* to determine next switch to go for the packet. 
        * per-flow state established prior to the exchange of data on VC using a *signaling protocol*

*  **Datagram**:
    *  a special type of packet:
        >  all identifying info of src and dest resides inside the packet itself (instead of in the packet switches)
    *  Larger packets, but lead to **connectionless** network (no more complicated signaling protocol, yeah)

*  **Message Boundaries** (or **Record Markers**):
    >  position or byte offset between one write and another
    *  indicate the position of sender's message boundaries at the receiver. 
    *  [CodeProject Explanation](http://www.codeproject.com/Articles/11922/Solution-for-TCP-IP-client-socket-message-boundary)
    *  TCP does *NOT* preserve msg boundaries. application must provide its own. 

*  **End-to-End Argument** and **Fate Sharing**:
    *  End-to-End:
      >  correctness and completeness can be achieved **ONLY** by involving the application or ultimate user of the communication system. 
      * important functions should *not* be implemented in low levels such as layers. 
      * low levels should *not* aim for perfection coz a perfect guess is unlikely to be possible
      * => a "dumb" network, a "smart" system
    *  Fate sharing:
      >  placing all the ncessary state to maintain an active communication association (e.g., virtual circuits) at the same location with the communicating endpoints. 
      *  either fail together or not at all ( car fail even only one tyre blowout )

