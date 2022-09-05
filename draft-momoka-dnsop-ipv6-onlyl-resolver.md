---

title: "IPv6 only resolver under NAT64"
abbrev: "delete this part if title is short."
docname: draft-momoka-dnsop-ipv6-onlyl-resolver-latest
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
    ins: yas-nyan
    name: Toyota Yasunobu
    organization: WIDE
    email: yasnyan@sfc.wide.ad.jp


normative:
  RFC6052:
  RFC6146:
    display: NAT64
  RFC6147:
    display: DNS64
  RFC1035:
  RFC3901:
  RFC4787:



informative:
  RFC7050:
  RFC3596:
  RFC6144:
  RFC4033:
  RFC4034:
  RFC4045:
  RFC4074:
  RFC4045:




--- abstract

By performing IPv4 to IPv6 translation, IPv6 only resolvers can operate in a NAT64 environment.
When a specific DNS zone is only served by a IPv4-only name server, the resolver will translate IPv4 to IPv6 in order to access the authoritative name server's IPv4 address via NAT64.
This mechanism allows an IPv6-only resolver to initiate communications to an authoritative name server.

--- middle

# Introduction

TODO Introduction

This document specifies how a IPv6 only resolver can perform IPv4 to IPv6 translation in order to utilize the NAT64 to access a IPv4 only authoritative name server.

# Motivation and Problem Solved
We want IPv6 only resolver.

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.

# Implementation Status
TODO

Unbound and Bind !!

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
