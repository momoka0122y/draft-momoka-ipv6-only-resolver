---
title: "IPv6 only iterative resolver utilising NAT64"
abbrev: IPv6 only Resolver
docname: draft-momoka-v6ops-ipv6-only-resolver-latest
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
    ins: Y. Toyota
    name: Toyota Yasunobu
    organization: Keio University/WIDE Project
    email: yasnyan@sfc.wide.ad.jp


normative:




informative:
  I-D.ietf-v6ops-ipv6-deployment:
    display: ietf-v6ops-ipv6-deployment
  I-D.draft-hunek-v6ops-nat64-srv:
    display: draft-hunek-v6ops-nat64-srv
  I-D.draft-XIE-v6ops-framework-md-ipv6only-underla:
    display: draft-XIE-v6ops-framework-md-ipv6only-underla



--- abstract

By performing IPv4 to IPv6 translation, IPv6 only iterative resolvers can operate in an IPv6-only environment.
When a specific DNS zone is only served by an IPv4-only authoritative server, the iterative resolver will translate the IPv4 address to IPv6 in order to access the authoritative server's IPv4 address via NAT64.
This mechanism allows IPv6 only iterative resolvers to initiate communications to IPv4 only authoritative servers.

--- middle

# Introduction


This document describes how an IPv6-only iterative resolver can use NAT64 {{!NAT64=RFC6146}} to connect to an IPv4 only authoritative server by performing IPv4 to IPv6 translation {{!RFC6052}}.
When a specific DNS zone is only served by an IPv4 only authoritative server (has only an A record), an IPv6 only iterative resolver cannot resolve that zone due to having no access to an IPv4 network.
However by performing IPv4 to IPv6 translation and utilizing the NAT64, accessing an IPv4 only authoritative server will be possible.


# Terminology

* Iterative resolver:    A DNS server that repeatedly makes non-recursive queries and follows referrals and/or aliases.  The iterative resolution algorithm is described in Section 5.3.3 of {{?RFC1034}}

* IPv6-only iterative resolvers :    Iterative resolvers that only have IPv6 connectivity.

* IPv6/IPv4 translator:     A device that translates IPv6 packets to IPv4 packets and vice versa. It is only required that the communication initiated from the IPv6 side be supported.

* IPv4-only authoritative server:    An authoritative server that only has IPv4 connectivity, or an authoritative server that only has an A record registered so can only be accessed by IPv4.

# Motivation and Problem Solved
Over the past decade, IPv6 capabilities have been widely deployed, and IPv6 traffic is now growing more quickly than IPv4 traffic.
An overview of IPv6 deployment status and how network operators are implementing IPv6 is provided by the document {{?I-D.ietf-v6ops-ipv6-deployment}}.
Most IPv6 deployments as of 2022 use a dual-stack strategy {{?RFC4213}}.
However, the deployment of IPv6-only networks is also in progress, as demonstrated by {{?I-D.draft-XIE-v6ops-framework-md-ipv6only-underla}}.
By operating an IPv6-only network and limiting IPv4 reachability to NAT64 devices, operators can reduce IPv4 usage and concentrate on IPv6 operations, which is generally believed to lower operational costs and optimize operations in comparison to a dual-stack environment.


An iterative resolver is one of the applications that requires IPv4 connectivity. As stated in BCP91 {{!RFC3901}}, “every recursive name server SHOULD be either IPv4-only or dual stack.”
This is because some authoritative servers do not support IPv6.
As of 2022, even some of the most frequently queried authoritative servers cannot be accessed via IPv6.
Without utilization of the NAT64, IPv6-only recursive resolvers need to forward queries to a dual stack recursive name server actually performing the iterative queries.


The current situation where an iterative resolver cannot be operated without IPv4 reachability may hinder the operation of a network's own iterative resolver in an IPv6-only network.
Therefore, this document describes how iterative resolvers can be used without any issues in IPv6-only networks by utilizing NAT64.





The NAT64/DNS64 mechanism is used to enable IPv6-only clients in a network to communicate with remote IPv4-only nodes. However, using literal IPv4 addresses instead of DNS names will fail (unless 464XLAT {{?RFC8683}} is used).
An iterative resolver cannot use the DNS64 because it is a service that uses literal IP addresses (and also because the DNS64 may depend on the resolver itself).
This problem can be solved by the iterative resolver converting IPv4 addresses to IPv6 by adding the Pref64::/n prefix, which instructs the NAT64 to convert the IPv6 packets to IPv4 packets.
With this implementation an iterative resolver can be operated even inside an IPv6-only network.

# Solution with existing protocols
This section provides the mechanism of an IPv6-only resolver utilizing the NAT64.
We'll assume we have one or more IPv6/IPv4 translator boxes {{!NAT64=RFC6146}} connecting an IPv6 network to an IPv4 network.
The NAT64 device provides translation service and bridges the two networks, allowing communication between IPv6-only hosts and IPv4-only hosts.
The IPv6-only resolver proposed in this document performes the IPv4 to IPv6 synthesis in order for the resolver to communicate with IPv4 servers via NAT64.
By using NAT64, this IPv6-only iterative resolver can be considered dual stack in the sense of {{!RFC3901}}.

## Finding an Authoritative server with only IPv4 addresses

Before the server sends queries it may sort the SLIST data structure described in {{?RFC1034}} to use the servers with IPv6 addresses first and use servers with only an IPv4 address to be used later.
If the resolver only finds an A record for the authoritative server, the resolver should perform address synthesis to the IPv4 address of the authoritative server.
It is not recommneded to synthesize IPv4 addresses of an authoritative server if the authoritative server also has an IPv6 address.

## Generation of the IPv6 Representations of IPv4 Addresses

### Obtaining the Pref64::/n of the NAT64

The iterative resolver can obtain the Pref64::/n used by the NAT64 of the network by either static configuration or by using discovery mechanisms.
Static configuration may be the most likely scenario given that the iterative resolver server may also serve as a DNS64 server.

The Port Control Protocol {{?RFC7225}} or Router Advertisements {{?RFC8781}} are two options the resolver has if it wants to use a discovery mechanism to find the Pref64::/n.
Using the mechanims described in {{?RFC7050}} or {{?I-D.draft-hunek-v6ops-nat64-srv}} may not function because these need a resolver to work.


### Performing the Synthesis

Performing the addres translation can be done by following Section 2.3 of {{!RFC6052}}.
After the synthesis is done the IPv6-only iterative resolver can send a query to the converted IPv6 address.

## Use of the iterative resolver as DNS64

Since the iterative resolver will be used inside an IPv6 only network, the server can also perform DNS64 {{!DNS64=RFC6147}} when a AAAA record is queried from a STUB resolver but the domain only has an A record.

# Deployment Notes
TODO


## Deployment Scenarios and Examples
In examples of past RFCs name resolvers have always had an IPv4 address.
For example all three use cases for DNS64 in RFC 6147 are dual-stack name servers, but it is necessary to consider the existence of an IPv6 single-stack full-service resolver with DNS64 capabilities.

~~~ drawing
            +---------------------+         +---------------+
            |IPv6 network         |         |    IPv4       |
            |           |  +-------------+  |  Internet     |
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
{: #example-topologies-in-RFC6147-1 title="Example network setup of the use of DNS64 described in RFC6147 Section7.1"}

~~~ drawing
            +---------------------+         +---------------+
            |IPv6 network         |         |    IPv4       |
            |           |     +--------+    |  Internet     |
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
{: #example-topologies-in-RFC6147-2 title="Example network setup of the use of DNS64 described in RFC6147 Section7.2"}

However in this document we consider an IPv6-only network where the iterative resolver is inside the IPv6 only network and does not have an IPv4 address. This is to contain IPv4 management to only the NAT64 device.

~~~ drawing
      +--------------------------+         +----------------------+
      | IPv6 network             |         |    IPv4              |
      |                          |         |  Internet            |
      |                          |         |                      |
      | +----------+             |         |  +--------------+    |
      | |IPv6-only |   |         |         |  |Authoritative |    |
      | |Iterative |   |         |         |  |server        |    |
      | |resolver  |---|   +------------+  |  +--------------+    |
      | +----------+   |---| IPv6/IPv4  |--|  192.0.2.1           |
      |                |   | Translator |  |                      |
      |                    +------------+  |                      |
      |                          |         |                      |
      +--------------------------+         +----------------------+
~~~
{: #ipv6oly-resolver-example-topology title="A network example this document refers to"}

# Security Considerations

TODO Security

Write about DNSSEC Validators and DNS64.

This algorithm does not alter any part of the DNS message but only change the packet type from IPv4 to IPv6 and the destination IP Address from an IPv4 address to the synthesised IPv6 address so there shouldn't be any problems with DNSSEC.



# IANA Considerations

This document has no IANA actions.

# Implementation Status
TODO: write this part and mail BIND.

Bind has an WIP branch.

https://gitlab.isc.org/isc-projects/bind9/-/merge_requests/6334/commits


Unbound has a PR from a contributor.
https://github.com/NLnetLabs/unbound/issues/721

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge people.

Thank you for reading this draft.
