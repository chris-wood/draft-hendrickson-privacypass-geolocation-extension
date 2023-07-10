---
title: "Privacy Pass Geolocation Hint Extension"
abbrev: "Privacy Pass Geolocation Hint Extension"
category: info

docname: draft-hendrickson-privacypass-geolocation-extension-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Security"
workgroup: "Privacy Pass"
keyword:
 - token
 - extensions
venue:
  group: "Privacy Pass"
  type: "Working Group"
  mail: "privacy-pass@ietf.org"
  github: "chris-wood/draft-hendrickson-privacypass-geolocation-extension"
  latest: "https://chris-wood.github.io/draft-hendrickson-privacypass-geolocation-extension/draft-hendrickson-privacypass-expiration-extension.html"

author:
 -
    fullname: Scott Hendrickson
    organization: Google
    email: "scott@shendrickson.com"
 -
    fullname: Christopher A. Wood
    organization: Cloudflare, Inc.
    email: caw@heapingbits.net

normative:
   AUTH-EXTENSIONS: I-D.wood-privacypass-auth-scheme-extensions
   AUTH-SCHEME: I-D.ietf-privacypass-auth-scheme
   EXTENDED-ISSUANCE: I-D.hendrickson-privacypass-public-metadata-issuance
   BASIC-ISSUANCE: I-D.ietf-privacypass-protocol
   ARCHITECTURE: I-D.ietf-privacypass-architecture


--- abstract

This document describes an extension for Privacy Pass that allows tokens
to encode geolocation hints.

--- middle

# Introduction

Some Privacy Pass token types support binding additional information to
the tokens, often referred to as public metadata. {{AUTH-EXTENSIONS}} describes
an extension parameter to the basic PrivateToken HTTP authentication scheme {{AUTH-SCHEME}}
for supplying this metadata alongside a token. {{EXTENDED-ISSUANCE}} describes
variants of the basic Privacy Pass issuance protocols {{BASIC-ISSUANCE}} that
support issuing tokens with public metadata. However, there are no existing
extensions defined to make use of these protocol extensions.

This document describes an extension for Privacy Pass that allows tokens
to encode geolocation hints. These hints can be used by origins that redeem
tokens to influence its behavior in various ways, such as determining the
content of HTTP responses.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Geolocation Hint Extension {#geohint-extension}

The geolocation hint extension is an extension used to convey rough geolocation hints
to origins that do not already have accurate or authoritative mappings for the IP
addresses of clients. This can be particularly useful for cases where IP geolocation
mappings have changed recently, or a client is using a VPN or proxy that may not be
commonly recognized by servers.

The value of this extension is an GeoHintTimestamp, defined as follows.

~~~
struct {
   opaque geo_hint<1..2^16-1>;
} GeoHint;
~~~

The GeoHintTimestamp fields are defined as follows:

- "geo_hint" is a string formatted as defined in {{Section 2.1.1 of !GEOFEED=RFC8805}}. It
  contains a comma-separated list of Alpha2code, Region, and City. The value SHOULD NOT
  contain a Postal Code.

As an example, a GeoHint structure corresponding to the "192.0.2.5,US,US-AL,Alabaster" entry
would be:

~~~
struct {
   opaque geo_hint<1..2^16-1>; // "US,US-AL,Alabaster"
} GeoHint;
~~~

# Security Considerations

Geolocation information can contribute to a client's fingerprint. Exposing precise geolocation
information can therefore lead to anonymity set partitioning, as described in {{ARCHITECTURE}}.
More general information regarding the use of extensions and their possible impact on client
privacy can be found in {{ARCHITECTURE}}.

Servers MUST NOT use IP Geolocation Client Hints for making security or access-control decisions,
as the value can be spoofed by a client. The hint is intended only for use in optimizing behavior.

# IANA Considerations

This document registers the following entry into the "Privacy Pass PrivateToken Extensions" registry.

- Expiration extension
   - Type: 0x0002
   - Name: Geolocation hint
   - Value: GeoHint value as defined in {{geohint-extension}}
   - Reference: This document
   - Notes: None

--- back

# Acknowledgments
{:numbered="false"}

This document received input and feedback from Jim Laskey.
