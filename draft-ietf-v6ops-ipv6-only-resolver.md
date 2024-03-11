---
title: "IPv6-only Capable Resolvers Utilising NAT64"
abbrev: IPv6-only Resolver
docname: draft-ietf-v6ops-ipv6-only-resolver-latest
category: info

ipr: trust200902
area: Ops
workgroup: v6ops
keyword:
  - DNS
  - IPv6
submissionType:
  IETF
stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
 -
    fullname:
      :: 山本 桃歌
      ascii: Momoka Yamamoto
    organization: The University of Tokyo/WIDE Project
    email: momoka.my6@gmail.com

 -
    fullname:
      :: 豊田 安信
      ascii: Yasunobu Toyota
    organization: Keio University/WIDE Project
    email: yas-nyan@sfc.wide.ad.jp

replaces: draft-momoka-v6ops-ipv6-only-resolver

normative:


informative:



--- abstract

This document outlines how IPv6-only iterative resolvers can use stateful NAT64 to establish communications with IPv4-only authoritative servers.
It outlines a mechanism for translating the IPv4 addresses of authoritative servers to IPv6 addresses to initiate communications.
Through the mechanism of IPv4-to-IPv6 address translation, these resolvers can operate in an IPv6-only network environment.
--- middle

# Introduction


This document describes how an IPv6-only iterative resolver can use stateful NAT64 {{!NAT64=RFC6146}} to connect to an IPv4-only authoritative server by performing IPv4-to-IPv6 address translation {{!RFC6052}} when generating a query.
When a specific DNS zone is only served by an IPv4-only authoritative server (which has only an A record), an IPv6-only iterative resolver cannot resolve that zone due to having no access to an IPv4 network.
However, by performing IPv4-to-IPv6 address translation and utilising the stateful NAT64, it will be possible to access an IPv4-only authoritative server.

This document is meant to exemplify how existing tools can be used to
allow IPv6-only resolvers to reach IPv4-only resolvers.
DNS is thus seen as an application that uses NAT64.

The document focuses on the exchanges between iterative resolvers and
authoritative resolvers but can be generalized to cover communications
between IPv6-only recursive resolvers and IPv4-only resolvers.

# Terminology

* Iterative resolver:    A DNS server that repeatedly makes non-recursive queries and follows referrals and/or aliases, as defined in DNS Terminology {{?RFC8499}}

* IPv6-only iterative resolvers:    Iterative resolvers with only IPv6 connectivity.

* IPv6/IPv4 translator:     A function that translates IPv6 packets to IPv4 packets and vice versa. It is only required that the communication initiated from the IPv6 side be supported.

* IPv4-only authoritative server:    An authoritative server that either has only IPv4 connectivity or is registered with only an A record, making it accessible solely via IPv4.

# Motivation and Problem Solved



An iterative resolver is one of the applications that require IPv4 connectivity. As stated in BCP91 {{!RFC3901}}, “every recursive name server SHOULD be either IPv4-only or dual stack.”
This is because some authoritative servers do not support IPv6.
As of 2023, even some of the most frequently queried authoritative servers cannot be accessed via IPv6.
Without the utilization of an IPv6/IPv4 translation mechanism, IPv6-only resolvers need to forward queries to a dual-stack recursive name server performing the iterative queries.


The current situation where an iterative resolver cannot operate without IPv4 reachability may hinder the operation of a network's own iterative resolver in an IPv6-only network.
Therefore, this document describes how iterative resolvers can be used without issues in IPv6-only networks by utilising NAT64 as an IPv6/IPv4 translation mechanism.





The NAT64/DNS64 mechanisms enable IPv6-only clients in an IPv6-only network to communicate with remote IPv4-only nodes. However, applications that rely upon IPv4 address literals instead of DNS names will fail (unless 464XLAT {{?RFC6877}} is used).
An iterative resolver is a service that uses literal IP addresses, so it cannot use the DNS64.
This problem can be solved by the iterative resolver converting IPv4 addresses to IPv6 addresses using the Pref64::/n NAT64 prefix and following the address translation algorithm in {{?RFC6052}}. In doing so, an IPv6 packet conveying the query is directed to a stateful NAT64 function that converts the IPv6 packet to an IPv4 packet.
With this implementation, an iterative resolver can be operated even inside an IPv6-only network.


## Deployment Scenarios and Examples

The deployment of IPv6-only networks is in progress, as demonstrated by {{?RFC9386}}.
By operating an IPv6-only network and limiting IPv4 reachability to NAT64 functions, operators can optimize IPv4 address usage and concentrate on IPv6 operations, which is generally believed to lower operational costs and optimize operations compared to a dual-stack environment.




In examples of past RFCs, name resolvers have always had an IPv4 address. For example, all three use cases for DNS64 in {{?RFC6147}} are dual-stack name servers.



~~~ drawing
            +---------------------+         +---------------+
            |IPv6 network         |         |    IPv4       |
            |              +-------------+  |  Internet     |
            |           |--| Name server |--|               |
            |           |  | with DNS64  |  |  +----+       |
            |  +----+   |  +-------------+  |  | H2 |       |
            |  | H1 |---|         |         |  +----+       |
            |  +----+   |   +------------+  |  192.0.2.1    |
            |           |---| IPv6/IPv4  |--|               |
            |           |   | Translator |  |               |
            |           |   +------------+  |               |
            |           |         |         |               |
            +---------------------+         +---------------+
~~~
{: #example-topologies-in-RFC6147-1 title="Example network setup of the use of DNS64 described in Section7.1 of RFC6147"}

~~~ drawing
            +---------------------+         +---------------+
            |IPv6 network         |         |    IPv4       |
            |                 +--------+    |  Internet     |
            |           |-----| Name   |----|               |
            | +-----+   |     | server |    |  +----+       |
            | | H1  |   |     +--------+    |  | H2 |       |
            | |with |---|         |         |  +----+       |
            | |DNS64|   |   +------------+  |  192.0.2.1    |
            | +----+    |---| IPv6/IPv4  |--|               |
            |           |   | Translator |  |               |
            |           |   +------------+  |               |
            |           |         |         |               |
            +---------------------+         +---------------+
~~~
{: #example-topologies-in-RFC6147-2 title="Example network setup of the use of DNS64 described in Section7.2 of RFC6147"}

However, it is necessary to consider the existence of an IPv6 single-stack full-service resolver.
In this document we consider an IPv6-only network where the iterative resolver is inside the IPv6-only network and does not have an IPv4 address.
This is to restrict IPv4 management to the NAT64 function.

~~~ drawing
      +--------------------------+         +----------------------+
      | IPv6 network             |         |    IPv4              |
      |                          |         |  Internet            |
      |                |         |         |                      |
      | +----------+   |         |         |  +--------------+    |
      | |IPv6-only |   |         |         |  |Authoritative |    |
      | |Iterative |   |         |         |  |server        |    |
      | |resolver  |---|   +------------+  |  +--------------+    |
      | +----------+   |---| IPv6/IPv4  |--|  192.0.2.1           |
      |                |   | Translator |  |                      |
      |                    +------------+  |                      |
      |                          |         |                      |
      +--------------------------+         +----------------------+
~~~
{: #ipv6oly-resolver-example-topology title="Network example referenced in this document with an IPv6-only iterative resolver"}

# Solution with Existing Protocols
This section describes the mechanism of an IPv6-only capable resolver utilising stateful NAT64.
We assume that one or more IPv6/IPv4 translators {{!NAT64=RFC6146}} are connecting an IPv6 network to an IPv4 network.
The stateful NAT64 provides translation service and bridges the two networks, allowing communication between IPv6-only hosts and IPv4-only hosts.
The IPv6-only capable resolver proposed in this document performs the IPv4 to IPv6 synthesis for the resolver to communicate with IPv4-only servers via stateful NAT64.
By using stateful NAT64, this IPv6-only iterative resolver aligns with the dual-stack requirements of BCP91 {{!RFC3901}}.

## Finding an Authoritative Server with only IPv4 addresses


Before initiating a query, an iterative resolver may prioritize authoritative servers with IPv6 addresses by sorting the SLIST data structure, as described in {{?RFC1034}}.
If the resolver finds only an A record for an authoritative server, the resolver should perform address synthesis to the IPv4 address of the authoritative server, converting IPv4 addresses to IPv6 by following the algorithm in {{?RFC6052}}.
With this the IPv6 packet carrying the query is routed to a stateful NAT64 function, which will convert the IPv6 packet with a destination IPv4-converted IPv6 address that matches the NAT64 prefix to an IPv4 packet.
It is NOT RECOMMENDED to synthesize IPv4 addresses of an authoritative server if it is reachable over IPv6.

## Generating IPv4-converted IPv6 Addresses

### Obtaining the Pref64::/n of a NAT64

The iterative resolver can obtain the Pref64::/n used by the network's stateful NAT64 either by static configuration or by using a discovery mechanism.

The Port Control Protocol {{?RFC7225}} or Router Advertisements {{?RFC8781}} are two options available to the resolver if it wishes to use a discovery mechanism to find the Pref64::/n.
Using the mechanisms described in {{?RFC7050}} does not work because they require a resolver to work.



### Performing Address Synthesis

The address translation algorithm is performed by following Section 2.3 of {{!RFC6052}}.
After the synthesis is done, the IPv6-only iterative resolver can send a query to the IPv4-converted IPv6 address.





# Security Considerations

The use of NAT64 for address translation does not affect DNSSEC operations as no part of the DNS message is altered.



# IANA Considerations

This document has no IANA actions.

# Implementation Status

BIND has a work-in-progress branch available at:

https://gitlab.isc.org/isc-projects/bind9/-/merge_requests/6334/commits


Unbound has this feature available for versions after  Unbound 1.18.0

https://nlnetlabs.nl/projects/unbound/download/#unbound-1-18-0.

--- back

# Acknowledgments
{:numbered="false"}

TODO: acknowledge people.

Thank you for reading this draft.
