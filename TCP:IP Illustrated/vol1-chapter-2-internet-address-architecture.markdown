# 2. The Internet Address Architecture

## 2.1 Intro

* **Numbering Plan**

* **ISP**

## 2.2 Expressing IP Addresses

* IPv4 32bit [0, 255] IPv6 128bit

* IPv6:
  
  * 8 *blocks* or "fields", each formed by 4 hex numbers, divided by colon (:)
  * Rules: [RFC4291, 5952]
    * leading 0 MUST be ommited: 0058 <=> 58
    * blocks of 0 ommited, replaced by "::"(double): 0:0:0 <=> :: (MUST use to replace most 0 for only once)
    * *IPv4-mapped IPv6 addr*: 10.0.0.1 <=> ::ffff:10.0.0.1 (4th field must be ffff)
    * lower 32 bit can be written using dotted-quad notation: ::0101:f001 <=> ::1.2.240.1
    * a-f must be lowercase
  * http://[IPv6 addr]:Port# (Note the brackets)

## 2.3 Basic IP Addr Structure

### 2.3.1 Classful Addressing

* *network* portion to identify which network, *host* portion to identify particular host given network portion.
* same network could have different number of hosts. ip addr not enough, how? **Classful Addressing**
* 5 classes:
  ![Classful Addressing from Wiki](vol1-chapter-2-classful-addressing.png)
  * C to small but B too big. 
  * Lasted only for a decade, abandoned because inconvenient to centrally coordinate allocation of new class everytime a new network added to the Internet. 

### 2.3.2 Subnet Addressing [RFC0950]

* Why changes? Hard to centrally allocate. 
* Solution? allocate a network number, divide it locally by sys admin.
* Implementation: original host field => subnet field (site-specific) + host field. 
* Tradeoff: routers and hosts in a site require new way to determine subnet field. (previously just derived by class)
* Note: site aorder router IPv4 address is not the same with the traffic it routed to. 

### 2.3.3 Subnet Masks

* Def: assignment of bits used by host or router to determine where the network portion ends.
* Same length to the corresponding IP, 32 or 128
* **Prefix Length**: /x, x is the contiguous number of 1 bit from left. /8 <=> 1111 1111 000..... (32bit)
* Usage:
  * apply mask to addr by using bitwise AND operation to get the prefix.
  * e.g: 128.32.1.14 & 255.255.255.0 = 128.32.1.14/24 (prefix)
* Just a local matter, outside don't worry about this. 

### 2.3.4 Variable-Length Subnet Masks (VLSM)

* Same host number in a subnetwork if we only use one subnet mask. 
* Want flexibility? Use different-length subnet masks!
* Tradeoff: More complicated configuration.

### 2.3.5 Broadcast Addresses

* A special addr in each IPv4 subnetwork is reserved to be **subnet broadcast address**
* Send a *directed datagram* to this addr, then all hosts in the subnetwork will receive this datagram ("broadcast")
* How to acquire addr? Invert mask, bitwise OR with addr. 
  * eg: 128.32.1.14 OR (NOT 255.255.255.0) = 128.32.1.14 OR 0.0.0.255 = 128.32.1.255 (broadcast addr)
* Dangerous! So now disabled by default. Routers not required to support this feature. 
* broadcast addr of 0.0.0.0 (*this* network) is 255.255.255.255. Never forwarded by router. 

### 2.3.6 IPv6 Addr and Interface Identifiers

* **scope** of an addr refers to the portion of the network where the addr can be used.

  1. node-local: only for same computer
  2. link-local: among same network link or IPv6 prefix 
  3. global: Internet

* link-local use **interface identifiers (IIDs)** for unicast IPv6 addr assignment:
  * normally just 64bits, come either directly from *EUI-64* or special process.
  * **Extended Unique Identifier (EUI)** 

    * starts with 24-bit **Organizationally Unique Identifier (OUI)** followed by 40-bit **extension identifier**
    * EUI-48 differs significantly from EUI-64 only in length
    * low-order 2 bits of first byte is called *u* and *g* bit. 

      * u indicates addr locally administrated
      * g indicates group or multicast addr

    * Process to form link-local IPv6 addr. 
      1. convert EUI-48 to EUI-64: insert FFFE in 4th and 5th bytes of EUI-64, copy rest. 
        * eg: 00-11-22-33-44-55 => 00-11-22-FF-FE-33-44-55
      2. Invert the u bit to form IID
      3. use reserved link-local prefix fe80::/10 to form the IPv6 addr. 

## 2.4 CIDR and Aggregation

* 3 Problems:
  1. class B go quickly
  2. 32-bit IPv4 addr not enough
  3. # of entries global routing table growing, decreasing routing performance

* for 1 & 3, remove class breakdown, aggregate hierarchically assigned IP addr

* for 2, IPv6

### 2.4.1 Prefixes

* **Classless Inter-Domain Routing (CIDR)**, eliminate predefined separation of network 
* similar to VLSM, called **CIDR Mask**
* Core Internet routers must be able to interpret and process masks
  * prefix => addr range
  * 0.0.0.0/0 => 0.0.0.0 - 255.255.255.255
  * 128.0.0.0/1 => 128.0.0.0 - 255.255.255.255
  * 128.0.0.0/24 => 128.0.0.0 - 128.0.0.255

### 2.4.2 Aggregation

* Router: datagram arrive, need to find out the next hop. 
  * too many entries in there! not efficient to look up the next hop
  * a driver at crossroad, facing innumerable signs

* **hierarchical routing**
  * tree-style routing
  * apply **route aggregation** to reduce entry number
    * 190.154.27.0/26 + 190.154.27.64/26 => 190.154.27.0/25
    * 190.154.27.192/26 + 190.154.27.128/26 => 190.154.27.128/25

## 2.5 Special-Use Addr

* not used in unicast addr, will not be routed by public Internet
* IPv4 special-use addr
  * 0.0.0.0/8 local network
  * 10.0.0.0/8 private network
  * 127.0.0.0/8 same computer
  * 192.168.0.0/16 private networks
  * 255.255.255.255/32 local network broadcast

### 2.5.1 IPv4/IPv6 Translators

* mostly under development
* *IPv4-embedded IPv6*, different schemes.  

### 2.5.2 Multicast Addresses

* node-local, link-local, site-local, global or administrative
* send one datagram, multiple receive
* **any-source multicast (ASM)** and **source-specific multicast (SSM)**
  * ASM has different src ip in a group, SSM uses only one sender per group

### 2.5.3 IPv4 Multicast Addr

* class D space has been reserved for multicast

(ommitted)

### 2.5.4 IPv6 Multicast Addr

(ommitted)

### 2.5.5 Anycast Addr

(ommitted)

## 2.6 Allocation

IANA allocates IP addresses. 

### 2.6.1 Unicast

* IANA delegates allocation authority to **regional Internet registries (RIRs)**
  * RIRs coordinate through **Number Resource Organization (NRO)**
  * [RIR on Wikipedia](http://en.wikipedia.org/wiki/Regional_Internet_registry)
  * APNIC for Asia

* RIR assigns addr blocks to ISP and smaller registries
* ISP provides customer *provider-aggregatable (PA)* addr, which are *non-portable*
* *provider-independent (PI)* addr also exists.
  * need additional routing, need pay additional fee etc. 
  * big site will be willing to do so to prevent *provider lock* (*renumbering* if change ISP)

* whois.arin.net or ripe.net/whois

### 2.6.2 Multicast

(ommitted) 

## 2.7 Unicast Address Assignment

### 2.7.1 Single Provider/No Network/Single Addr

Simple, ommitted.

### 2.7.2 Single Provider/Single Network/Single Addr

Home route, LAN, WLAN. skipped.

### 2.7.3 Single Provider/Multiple Network/Multiple Addr

* For Enterprise. 
* **Demilitarized zone network (DMZ network)** used to attach servers that can be accessed outside. 
* Some for outside access, rest are given to NAT router.
  * NAT rewrite datagrams entering and leaving internal network

### 2.7.4 Multiple Providers/Multiple Networks/Multiple Addresses

* **multihoming**: more than one ISP to provide stable service. 
* When PA, Which IP addr to use if more than one ISP? **longest matching prefix**: 137.164/16 > 12/8, more specific
* When PI, more symmetric, but no aggregation. 

### 2.8 Attacks Involving IP Addresses

(skipped)

## 2.9 Summary

(skipped)
