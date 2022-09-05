---
title: "IPv6 only resolver under NAT64"
abbrev: IPv6 only Resolver
docname: draft-momoka-dnsop-ipv6-only-resolver-latest
category: info

ipr: trust200902
area: Ops
workgroup: dnsop
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

By performing IPv4 to IPv6 translation, IPv6 only resolvers can operate in a NAT64 environment.
When a specific DNS zone is only served by a IPv4-only name server, the resolver will translate IPv4 to IPv6 in order to access the authoritative name server's IPv4 address via NAT64.
This mechanism allows an IPv6-only resolver to initiate communications to an authoritative name server.

--- middle

# Introduction

TODO Introduction

This document specifies how a IPv6 only resolver can perform IPv4 to IPv6 translation {{!RFC6052}} in order to utilize the NAT64 {{!NAT64=RFC6146}} to access a IPv4 only authoritative name server.
This mechanism allows an IPv6-only resolver (i.e., a host with a networking stack that only implements IPv6, a host with a
networking stack that implements both protocols but with only IPv6
connectivity) to initiate communications to an IPv4-only authorative name server.


# Motivation and Problem Solved
TODO

write something like...

We want IPv6 only resolver. Because there are more IPv6 only networks.

But RFC3901 says resolvers needs IPv4.

This mechanism allows IPv6 only resolvers thus more resolvers in IPv6 only networks.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Normative Specification

## Generation of the IPv6 Representations of IPv4 Addresses
### Resolveing AAAA Queries to an authoritive name server.
TODO:

To know  if the server has IPv6, 5.1 of RFC6147
### Obtaining the Pref64::/n
The resolver can know the Pref64::/n by static configuration
(most likely because this resolver may also do DNS64 Network Address Translation to the IPv4 address to the domain with only a A record)
or by using RFC7225 or RFC8781.
Using RFC7050 or draft-hunek-v6ops-nat64-srv may not work because these need a resolver to work.
### Performing the Synthesis



# Deployment Notes
I don't know any notes to add here...


# Deployment Scenarios and Examples


# Security Considerations

TODO Security


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

TODO acknowledge.
