---
# Using https://github.com/httpwg/http-extensions/blob/main/archive/draft-ietf-httpbis-priority.md?plain=1 as example.


title: "IPv6 only resolver under NAT64"
# abbrev: "delete this part if title is short."
docname: draft-momoka-dns0p-ipv6-onlyl-resolver-latest
category: info

ipr: trust200902
area: Ops
workgroup: TODO dnsop
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

# Normative references specify documents that must be read to understand or implement the technology in the new RFC, or whose technology must be present for the technology in the new RFC to work. An informative reference is not normative; rather, it only provides additional information.
normative:
# 6052 7050 


informative:



--- abstract

By performing IPv4 to IPv6 translation, IPv6 only resolvers can operate in a NAT64 environment.
When a specific DNS zone is only served by an IPv4-only name server, the resolver will translate IPv4 to IPv6 in order to access the authoritative name server's IPv4 address via NAT64.


--- middle

# Introduction

TODO Introduction


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
