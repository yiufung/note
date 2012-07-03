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
    *  **latency**: upper bound on time. Gives predictable service. 

*  **Packet Switching**:
    *  chunks from different sources be mixed together and pull
 apart later, called **multiplexing**. 
    *  path subject to change. 
    *  Advantages: resilient (free from worry of physical attack) and
 better utilization of network links because of **statistical
 multiplexing**

*  Packets switch receives packets: 
    *  need to *schedule* the packets
    *  stored in *buffer memory* (or *queue*), processed in FCFS (or FIFO)
      1. **Statistical Multiplexing**:
          *  FIFO + on-demand scheduling.
          *  Pro: High utilization, as busy as it can be. 
          *  Con: Limited predictability.
      2.  **Time-Division Multiplexing**:
      3.  **Static Multiplexing**:
          *  reserve certain amount of time or other resources for each connection
          *  Pro: Predictability
          *  Con: Poor utilization

*  **Connection-Oriented** networks:
    *  Build on circuits or packets. 
    *  Virtual Circuits (VC) can be implemented atop connection oriented packets. Basis for X.25 protocol. 
    *  *state* be stored in each switch for each connection
    *  each packet carries small overhead info about the state
        * in X.25, 12-bit *logical channel identifier* (LCI, or *logical channel number*, LCN)
    *  at each switch, use LCI and *per-flow state* to determine next switch to go for the packet. 
        * per-flow state established prior to the exchange of data on VC using a *signaling protocol*

*  **Datagram**:

    *  a special type of packet:

        >  all identifying info of src and dest resides inside the packet itself (instead of in the packet switches)

    *  Larger packets, but lead to **connectionless** network (no more complicated signaling protocol, yeah)

*  **Message Boundaries** (or **Record Markers**)
    
    >  position or byte offset between one write and another
     
     *  indicate the position of sender's message boundaries at the receiver. 
     *  [Code Project Explanation](http://www.codeproject.com/Articles/11922/Solution-for-TCP-IP-client-socket-message-boundary)
     *  TCP does *NOT* preserve msg boundaries. application must provide its own. 

### 1.1.2  End-to-End Argument and Fate Sharing:

* **End-to-End Argument**:

  >  correctness and completeness can be achieved **ONLY** by involving the application or ultimate user of the communication system. 

  * important functions should *not* be implemented in low levels such as layers. 
  * low levels should *not* aim for perfection coz a perfect guess is unlikely to be possible
  * => a "dumb" network, a "smart" system

*  **Fate sharing**:

    >  placing all the necessary state to maintain an active communication association (e.g., virtual circuits) at the same location with the communicating endpoints. 

    *  either fail together or not at all ( car fails even only one tyre blowout )

    *  ? allows VC to remain active even if connectivity within network has failed for a modest time. 

*  Misconception among End-to-end, Fate Sharing and Stateless network:

  1. End-to-end as stated in [Saltzer/Reed/Clark Paper](http://www.reed.com/dpr/locus/Papers/EndtoEnd.html):

      > The principle, called the end-to-end argument, suggests that functions placed at low levels of a system may be redundant or of little value when compared with the cost of providing them at that low level. Examples discussed in the paper include bit error recovery, security using encryption, duplicate message suppression, recovery from system crashes, and delivery acknowledgement. Low level mechanisms to support these functions are justified only as performance enhancements.
    
        * E2E emphasizes that, a function can only be correctly and completely implemented with the help of the application standing at the end points of the communication system, but the communication system *itself* cannot provide such function. 

  2. Fate sharing as stated in [Design Philosophy of the DARPA Internet Protocols](http://ccr.sigcomm.org/archive/1995/jan95/ccr-9501-clark.pdf)

    > state information which describes the on-going conversation must be protected. Specific examples of state information would be the number of packets transmitted, the number of packets acknowledged .. If the lower layers of the architecture lose this information, they will not be able to tell if data has been lost, and the application layer will have to cope with the loss of synchrony. .. In some network architectures, this state is stored in the intermediate packet switching nodes of the network. .. The alternative, which this architecture chose, is to take this information and gather it at the endpoint of the net, at the entity which is utilizing the service of the network. I call this approach to reliability "fate-sharing." The fate-sharing model suggests that it is acceptable to lose the state information associated with an entity if, at the same time, the entity itself is lost.
    
      * Fate sharing empahasizes that, "state info which describes *on-going* conversation" be gathered at *endpoint* of the net.

  * Thanks for [J. Noel Chiappa's clear explanation](http://mercury.lcs.mit.edu/~jnc/tech/end_end.html)

### 1.1.3 Error Control and Flow Control

* Error Control
    *  causes: hardware, radiation, blah blah
    *  E2E argument and fate sharing suggests that error control to be implemented *close to or within applications*. 
    *  mathematical codes can detect repair bit errors, but more generally, just retransmit the packet. 
    *  reliable, in-order delivery is expensive, so Internet Protocol uses **best-effort delivery**
        *  errors detected using *checksums*

* Flow Control 
    *  TCP handles rate control at end hosts. 

## 1.2 Design and Implementation

### 1.2.1 Layering

Each layer for different facet of communications. 

**Open Systems Interconnection (OSI)**:

  1. Physical -> data rates, bit encoding, low-lever error detection
  
  2. Link     -> methods for communication across single link. error detection mostly lies here. e.g., Ethernet, Wi-Fi
    
  3. Network or Internetwork -> methods for communicating in a multihop fashion across different type of link networks. e.g., IP datagram
    
    * 1-3 is for all networked devices, 4-7 are (in therory) implemented only by hosts. 
    
  4. Transport ->  connections or associations between multiple programs running on same computer system. e.g., Internet TCP
    
  5. Session -> for multiple connections constituting a communication session
    
  6. Presentation -> for expressing data formats and translation rules for application. e.g., conversion of EBCIDC to ASCII
    
  7. Application -> accomplishing user-initiated task. e.g., FTP, Skype

### 1.2.2 Multiplexing, Demultiplexing, Encapsulation in Layered Implementation

*  **Protocol Multiplexing** allows multiple different protocols to coexist on the same infrastructure. 
*  **Multiplexing** can occur at different layers and an *identifier* is used to determine which protocol belongs together
*  **Protocol Data Unit** is **encapsulated** and carried by a lower layer. N-1 identifier is used to determine correct layer N receiving protocol. 
*  End-to-end protocols: protocols that are above network layer
*  Hop-by-hop protocol: provided by network layer
*  Switch or bridge is not considered as "intermediate system" as they are transparent to the network-layer protocol

*  **Multi-homed**: any system with multiple interfaces.
*  Applications should not and do not care about detailed lower-layer protocol heterogeneity and physical layout. 

## 1.3 The Architecture and Protocols of TCP/IP Suite

### 1.3.1 The ARPANET Reference Model

* IP datagram: referred as "packet" when context is clear. 
  * Fragmentation: fit large packets into link-layer PDUs (called **frames**) that may be smaller. Divided into **fragments** and **reassemble** later. 
  * Layer 3 sender and recipient ip addresses contained here: 32bits for IPv4, 128bits for IPv6

* **Forwarding**:
  * process of determining where each datagram should be sent and sending it to next hop
  * 3 types: *unicast* (one host only), *broadcast* (all hosts in a network) and *multicast* (a set of hosts belongs to a multicast group)

* **Internet Control Message Protocol (ICMP)**:

  > used by IP layer to exchange error messages and other vital information with the IP layer in another host or router

  * ICMPv4 and v6 corresponding to IPv4 and v6.
  * traceroute and ping use info from this layer. 

* **Internet Group Management Protocol (IGMP)**:
  > used with multicast addressing and delivery to manage which hosts are members of a multicast group

* Transport Layer:
 
  1. **Transmission Control Protocol**:
      * packet loss, duplication, reordering
      * connection-oriented, does not preserve message boundaries
      * reliable flow of data between two hosts. 

  2. **User Datagram Protocol**:
      * allow applications preserve message boundaries if there is no rate control or error control
      * not reliable. allows datagrams to be sent, but no guarantee that it will reach the other end. 
      * app add reliability itself.

  3. *Datagram Congestion Control Protocol (DCCP)*:
      > connection-oriented exchange of unreliable datagrams but with congestion control

  4. *Stream Control Transmission Protocol (SCTP)*:
      > reliable delivery but does not require sequencing of data to be strictly maintained. 

### 1.3.2 Multiplexing, Demultiplexing and Encapsulation in TCP/IP:

1. IP get a datagram and checks destination IP address
2. If dest IP addr exists, check 8-bit IPv4 *Protocol* field to determine what protocol to invoke next. 
    * 4 for IPv4, 6 for TCP, 17 for UDP
3. Passed to Transport Layer for further process. 

### 1.3.3 Port Numbers

* 16-bit nonnegative integers. 
* each IP address has 65536 associated port numbers for each transport protocol
* used to determine which application to receive the data
* Server binds to port number, clients establish connections to that port
* 3 types: well-known (0-1023), registered (1024-49151), dynamic/private (49152-65535)
  * servers wish to bind to well-known to have root access
  * SSH 22, FTP 20/21, Telnet 23, SMTP 25, DNS 53, HTTP 80, HTTPS 443

### 1.3.4 Names, Addr, DNS

* DNS: mapping between host names and IP addrs. 

## 1.4 Internets, Intranets and Extranets

* internet: multiple networks connected together using a common protocol suite
* Internet: collection of hosts around the world that can communicate with each other using TCP/IP. 
  * Internet is internet, internet is NOT Internet.

* **Routers**: used to connect networks. 
  * called *gateways* historically

* intranet: private internetwork, usually by business
* extranet: a network containing servers accessible to partners using Internet

## 1.5 Designing Applications

* two types: client/server and peer-to-peer

### 1.5.1 Client/Server

* two types of server: **iterative** and **concurrent**:
  1. iterative:

      * process: 
      	1. wait for client request
      	2. process
      	3. send back response
      	4. go to 1. 
      * problem: 
      	* what if 2 takes a long time? no other clients are serviced. 

  2. concurrent:

      * process:
        1. wait for client request
        2. start new server instance to handle this request. this instance is terminated when request is done. original server continues to 3. 
        3. go to 1. 

* nowadays most are concurrent. 

### 1.5.2 Peer-to-peer

* No single server, distributed, capable of forwarding requests 
* e.g., Skype, BT. 
* Application receives an incoming request, determine whether it can respond. If not, forward to some other peer. 
* set of p2p applications form an **overlay network**
* *discovery problem*: how does one peer find which other peers can provide the service? 
  * solution: each client is configured with addrs and port numbers of some peers that are likely to be operating. 

### 1.5.3 APIs
* most popular API: **sockets** or **Berkeley sockets**

## 1.6 Standardization Process
* **Internet Engineering Task Force (IETF)**
  * 3 times a year to discuss. Open to anyone but not free. 
  * elects leadership groups called **Internet Architecture Board (IAB)** and **Internet Engineering Steering Group (IESG)**
  * IAB provide architectural guidance, IESG decision-making on new standards creation and approval. 
* **Internet Research Task Force (IRTF)** and **Internet Society (ISOC)**

### 1.6.1 Request for Comments (RFC)

* Every official standard published as RFC. 
  * publisher: *RFC editor* 
* All RFCs are not standards. 

### 1.6.2 Other Standards

* IEEE for below Layer 3, W3C for web tech, ITU for telephone and cellular networks 

## 1.7 Implementations and Software Distributions

* de facto standard TCP/IP implementation from **Computer Systems Research Group (CSRG)** at UCB.

## 1.8 Attacks involving Internet Architecture

* **Spoofing**: insert ip addr into src IP addr field of datagram. 
  * hard to perform **attribution**, i.e, to determine the origin of a datagram. 

* **Denial-of-service (DoS)**
  * sending so many IP datagrams such that the server cannot perform useful work. 
  * **Distributed Denial-of-service (DDoS)**: using many computers to clog the network, such that no other packets can be sent. 

* Unauthorized access
  * exploit implementation bugs to take control of system, turning it into *bot* or *zombie*
  * *black hats* install malware in a distributed fashion (*botnets*). 

* Encryption:
  * IP does not perform encryption, easy to leak private information

## 1.9 Summary
* nil
