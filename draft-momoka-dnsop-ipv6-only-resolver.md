---
title: "IPv6 only recursive name server under NAT64"
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

By performing IPv4 to IPv6 translation, IPv6 only recursive name servers can operate in a NAT64 environment.
When a specific DNS zone is only served by a IPv4 only name server, the recursive name server will translate IPv4 to IPv6 in order to access the authoritative name server's IPv4 address via NAT64.
This mechanism allows an IPv6 only recursive name server to initiate communications to an authoritative name server.

--- middle

# Introduction

TODO Introduction


This document describes how an IPv6 only recursive name server can use NAT64 (!NAT64=RFC6146)
to connect to an IPv4 only authoritative name server by performing IPv4 to IPv6 translation (!RFC6052).
This mechanism allows an IPv6 only recursive name server (i.e., a host with a networking stack that only implements IPv6, or a host with a
networking stack that implements both protocols but with only IPv6 connectivity)
to initiate communications to an IPv4-only authorative name server.


# Motivation and Problem Solved
TODO



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


### Resolveing AAAA Queries to an authoritive name server.
TODO:

To know if the server has IPv6, follow 5.1 of RFC6147.

### Obtaining the Pref64::/n
The resolver can recognize the Pref64::/n using static configuration or other or by using RFC7225 or RFC8781.
Because these require a resolver to function, using RFC7050 or draft-hunek-v6ops-nat64-srv connot be successful.


### Performing the Synthesis
TODO

Use {{!RFC6052}}.

## Use of the recursive name server as DNS64

TODO:

Since the recursive name server will be used inside an IPv6 only network, the server can also perform DNS64 {{!RFC6147}}.


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
