---
title: "Carrying a Generic Identifier in IPv6 Extension Header"
abbrev: "Generic Identifier"
category: std

docname: draft-iurman-6man-generic-id-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Internet"
workgroup: "IPv6 Maintenance"
keyword:
venue:
  group: "IPv6 Maintenance"
  type: "Working Group"
  mail: "ipv6@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/ipv6/"
  github: "IurmanJ/draft-iurman-6man-generic-id"
  latest: "https://IurmanJ.github.io/draft-iurman-6man-generic-id/draft-iurman-6man-generic-id.html"

author:
 -
    ins: J. Iurman
    name: Justin Iurman
    organization: ULiege
    street:
    - Institut Montefiore B28
    - Allée de la Découverte 10
    code: 4000
    city: Liège
    country: Belgium
    email: justin.iurman@uliege.be

normative:

informative:


--- abstract

Some recent use cases seem to have a need for carrying IDs within packets. Two
examples are {{?I-D.draft-ietf-6man-enhanced-vpn-vtn-id}} and
{{?I-D.draft-li-6man-topology-id}}. While they might perfectly make sense on
their own, each document requires IANA to allocate a new code point for a new
option, which could quickly exhaust the allocation space if similar designs are
proposed in the future. As an example, one might need an 8-bit ID, while another
one might need a 24-bit, 32-bit, or 64-bit ID. Or, even worse, one might need a
32-bit ID in a specific context, while someone else might also need a 32-bit ID
in another context. Therefore, allocating a new code point for each similar
option is probably not the way to go.


--- middle

# Introduction

Some recent use cases seem to have a need for carrying IDs within packets. Two
examples are {{?I-D.draft-ietf-6man-enhanced-vpn-vtn-id}} and
{{?I-D.draft-li-6man-topology-id}}. While they might perfectly make sense on
their own, each document requires IANA to allocate a new code point for a new
option, which could quickly exhaust the allocation space if similar designs are
proposed in the future. As an example, one might need an 8-bit ID, while another
one might need a 24-bit, 32-bit, or 64-bit ID. Or, even worse, one might need a
32-bit ID in a specific context, while someone else might also need a 32-bit ID
in another context. Therefore, allocating a new code point for each similar
option is probably not the way to go.

This document proposes a solution to carry IDs generically to avoid the problem
mentioned previously. Two new Hop-by-Hop and Destination options are defined,
called "Generic ID Option" and "Generic Context-ID Option". Both are defined and
explained in this document.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# New IPv6 Destination and Hop-by-Hop Options

## Generic ID Option

For simple use cases where an ID is carried without extra fields and without any
specific context, a new option type "Generic ID" is defined to carry such ID
generically in IPv6 packets, as defined below:

	 0                   1                   2                   3
	 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
		                        +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
		                        |  Option Type  |  Opt Data Len |
	+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	|                                                               |
	~                  Generic ID (variable length)                 ~
	|                                                               |
	+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

		           Figure 1. Generic ID Option

where:

- Option Type:  8-bit option type identifier as defined in
       [](#iana-considerations).

- Opt Data Len:  8-bit unsigned integer.  Length of the Generic ID field, in
       octets.

- Generic ID:  variable length field.

Note: as an example, {{?I-D.draft-li-6man-topology-id}} should use this option
to carry the 16-bit ID it defines.

## Generic Context-ID Option

For other use cases where an ID is carried with extra fields or when a context
is required, a new option type "Generic Context-ID" is defined to carry such ID
generically in IPv6 packets, as defined below:

	 0                   1                   2                   3
	 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
		                        +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
		                        |  Option Type  |  Opt Data Len |
	+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	|                         Context-Type                          |
	+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	|                                                               |
	~                Context Data (variable length)                 ~
	|                                                               |
	+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

		       Figure 2. Generic Context-ID Option

where:

- Option Type:  8-bit option type identifier as defined in
       [](#iana-considerations).

- Opt Data Len:  8-bit unsigned integer.  Length of this option, in octets, not
       including the first 2 octets.

- Context-Type:  32-bit field as defined in [](#context-type).

- Context Data:  variable length field. Context-Type-specific data.

Note: as an example, {{?I-D.draft-ietf-6man-enhanced-vpn-vtn-id}} should use
this option to carry the 16-bit ID and flags it defines.


# IANA Considerations

This document requests the following IPv6 Option Type assignments from the
Destination Options and Hop-by-Hop Options sub-registry of Internet Protocol
Version 6 (IPv6) Parameters.

http://www.iana.org/assignments/ipv6-parameters/ipv6-parameters.xhtml#ipv6-parameters-2

	Binary Value     Description                   Reference
	act chg rest
	--------------------------------------------------------------
	00   0  TBD      Generic ID Option             [This document]
	00   1  TBD      Generic Context-ID Option     [This document]


This document also requests IANA to define a registry group named "Generic
Context-ID".

This group includes the following registries:

- Context-Type


The subsequent subsections detail the registries therein contained.

## Context-Type

This registry defines (2^32 - 1) code points for the Context-Type field, to
identify the type of context. The following code points are defined in this
document:

- 0:  Reserved
- 1:  VTN {{?I-D.draft-ietf-6man-enhanced-vpn-vtn-id}}


Other code points are available for assignment via the "IETF Review" process, as
per {{?RFC8126}}.

New registration requests MUST use the following template:

- Name:  name of the newly registered Context-Type.
- Code point:  desired value of the requested code point.
- Reference:  reference to the document that defines the new Context-Type.


# Security Considerations

As this document describes new options for IPv6, these are similar to the
security considerations of {{?RFC8200}} and the weakness documented in
{{?RFC8250}}.

This document does not define the data contents of custom Generic Context-ID
options.  These custom options will have security considerations corresponding
to their defined data contents that need to be described where those formats are
defined.


--- back

# Acknowledgments
{:numbered="false"}

