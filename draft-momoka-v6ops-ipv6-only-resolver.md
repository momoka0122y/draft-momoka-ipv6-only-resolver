---
title: "IPv6 only recursive resolver under NAT64"
abbrev: IPv6 only Resolver
docname: draft-momoka-v6ops-ipv6-only-resolver-latest
category: info

ipr: trust200902
area: Ops
workgroup: v6ops
keyword:
  - DNS
  - IPv6

stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
 -
    fullname:
      :: 山本 桃歌
      ascii: Momoka Yamamoto
    organization: WIDE
    email: momoka.my6@gmail.com

 -
    ins: Y. Toyota
    name: Toyota Yasunobu
    organization: WIDE
    email: yasnyan@sfc.wide.ad.jp


normative:




informative:





--- abstract

By performing IPv4 to IPv6 translation, IPv6 only recursive resolvers can operate in a NAT64 environment.
When a specific DNS zone is only served by an IPv4 only name server, the recursive resolver will translate the IPv4 address to IPv6 in order to access the authoritative name server's IPv4 address via NAT64.
This mechanism allows IPv6 only recursive resolvers to initiate communications to IPv4 only authoritative name servers.

--- middle

# Introduction


This document describes how an IPv6 only recursive resolver can use NAT64 {{!NAT64=RFC6146}} to connect to an IPv4 only authoritative name server by performing IPv4 to IPv6 translation {{!RFC6052}}.
When a specific DNS zone is only served by an IPv4 only name server, an IPv6 only recursive resolver cannot resolve that zone due to having noaccess to an IPv4 network.
However by performing IPv4 to IPv6 translation {{!RFC6052}} and utilizing the NAT64 {{!NAT64=RFC6146}} this will be possible.

This mechanism allows an IPv6 only recursive resolver
(i.e., a host with a networking stack that only implements IPv6, or a host with a networking stack that implements both protocols but with only IPv6 connectivity)
to initiate communications to an IPv4-only authoritative name server.


# Motivation and Problem Solved
IPv6 capabilities have been widely deployed during the past decade with IPv6 traffic growing faster than IPv4.

The operation of IPv6 only network is increasing.

It is generally supposed that running an IPv6-only network would reduce operational expenditures and optimize operations as compared to a dual-stack environment.
In this case IPv4 management will be minimized to the use of IPv4 as a Service(IPv4aaS) for IPv4 service continuity.


In examples of past RFCs name resolvers have always had as IPv4 address.

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
{: #example-topologies-in-RFC6147-1 title="Example topology of use of DNS64 described in RFC6147 Section7.1"}

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
{: #example-topologies-in-RFC6147-2 title="Example topology of use of DNS64 described in RFC6147 Section7.2"}

However in this RFC we use a topology where the recursive resolver inside the IPv6 only network and not have an IPv4 address. This is to contain IPv4 management to only IPv4aaS.

~~~ drawing
             +---------------------+         +---------------+
             |IPv6 network         |         |    IPv4       |
             |                     |         |  Internet     |
             |           |         |         |               |
             | +-----+   |         |         |  +----+       |
             | | H1  |   |         |         |  | H2 |       |
             | |with |---|         |         |  +----+       |
             | |DNS64|   |   +------------+  |  192.0.2.1    |
             | +----+    |---| IPv6/IPv4  |--|               |
             |           |   | Translator |  |               |
             |           |   +------------+  |               |
             |           |         |         |               |
             +---------------------+         +---------------+
~~~
{: #ipv6oly-resolver-example-topology title="The topology we want to achieve"}

However, according to BCP91 {{!RFC3901}}, “every recursive name server SHOULD be either IPv4-only or dual stack.” Meaning recursive resolvers should not be IPv6 only. This is because some authoritative servers do not support IPv6.

Currently even some of the most widely queried authoritive servers still only have an IPv4 address and cannot be queried by IPv6.


We propose an IPv6-only network-compatible recursive resolver implementation. With this implementation, the IPv6-only recursive resolver will be able to send queries to IPv4-only authoritative name servers. This is accomplished by the resolver converting IPv4 addresses to IPv6 by adding the Pref64::/n prefix, which instructs the NAT64 to convert the IPv6 packets to IPv4 packets.



write something like...

We want IPv6 only resolver. Because there are more IPv6 only networks.

But RFC3901 says resolvers needs IPv4.

This mechanism allows IPv6 only resolvers thus more resolvers in IPv6 only networks.


By limiting IPv4 reachability to NAT64 devices in an IPv6 single-stack network, IPv4 usage is reduced and operators are free to focus on IPv6 operations.
All three use cases for DNS64 in RFC 6147 are dual-stack name servers, but it is necessary to consider the existence of an IPv6 single-stack full-service resolver with DNS64 capabilities.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Normative Specification

## Generation of the IPv6 Representations of IPv4 Addresses


### Resolveing AAAA Queries to an authoritative name server.
TODO:

To know if the server has IPv6 or has only IPv4, follow 5.1 of RFC6147.

TODO

This mechanism may change for doing so for authoritative servers. Will make sure to document that.

### Obtaining the Pref64::/n
The resolver can recognize the Pref64::/n using static configuration or other or by using RFC7225 or RFC8781.
Because these require a resolver to function, using RFC7050 or draft-hunek-v6ops-nat64-srv connot be successful.


### Performing the Synthesis
TODO

Use {{!RFC6052}}.

## Use of the recursive resolver as DNS64

TODO:

Since the recursive resolver will be used inside an IPv6 only network, the server can also perform DNS64 {{!RFC6147}}.


# Deployment Notes
TODO


# Deployment Scenarios and Examples
TODO

Add deployment example topologoies.

The resolver will be inside the IPv6 only network and not on the edge with IPv4.

# Security Considerations

TODO Security

DNSSEC Validators and DNS64.


# IANA Considerations

This document has no IANA actions.

# Implementation Status
TODO: write this part and mail Bind.

Bind has an WIP branch.

https://gitlab.isc.org/isc-projects/bind9/-/merge_requests/6334/commits


Unbound has a PR from a contributor.
https://github.com/NLnetLabs/unbound/issues/721

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge people.
