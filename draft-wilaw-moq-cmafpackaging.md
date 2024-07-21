---
title: "CMAF Packaging for moq-transport"
category: info

docname: draft-wilaw-moq-cmafpackaging-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Applications and Real-Time"
workgroup: "Media Over QUIC"
keyword:
 - moq
 - cmaf
venue:
  group: "Media Over QUIC"
  type: "Working Group"
  mail: "moq@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/moq/"
  github: "wilaw/moq-cmaf-packaging"
  latest: "https://wilaw.github.io/moq-cmaf-packaging/draft-wilaw-moq-cmafpackaging.html"

author:
 -
    fullname: "Will Law"
    organization: "Akamai"
    email: "wilaw@akamai.com"
 -
    fullname: "Luke Curley"
    email: kixelated@gmail.com

normative:
  MoQTransport: I-D.ietf-moq-transport
  ISOBMFF:
    title: "Information technology -- Coding of audio-visual objects -- Part 12: ISO Base Media File Format"
    date: 2015-12
  CMAF:
    title: "Information technology -- Multimedia application format (MPEG-A) -- Part 19: Common media application format (CMAF) for segmented media"
    date: 2020-03
  CENC:
    title: "International Organization for Standardization - Information technology - MPEG systems technologies - Part 7: Common encryption in ISO base media file format files"
    date: 2020-12

informative:


--- abstract

Packaging CMAF content for use with MoQ Transport {{MoQTransport}}

--- middle

# Introduction

This specification defines an interoperable method of transmitting CMAF {{CMAF}} compliant media content over Media Over QUIC Transport (MOQT) {{MoQTransport}}. Multiple mappings are supported, including mapping complete Groups of Pictures (GOPS) {{ISOBMFF}} or individual frames to MoQTransport Objects. This specification is intended to be referenced by MOQT-compliant Streaming Formats.

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 [RFC2119].


# Media Object Payload {#objectpayload}
This specification defines a direct mapping between CMAF Tracks ( {{CMAF}} Sect 3.2.1)  and MOQT tracks ({{MoQTransport}} Sect 2.3). As a consequence, the MOQT Object payload:

* MUST consist of a Segment Type Box (styp) followed by any number of media fragments. Each media fragment consists of a Movie Fragment Box (moof) followed by a Media Data Box (mdat). The Media Fragment Box (moof) MUST contain a Movie Fragment Header Box (mfhd) and Track Box (trak) with a Track ID (`track_ID`) matching a Track Box in the initialization fragment.
* MUST contain a single {{ISOBMFF}} track.
* MUST contain media content encoded in decode order. This implies an increasing decoding time stamp (DTS).
* MAY contain any number of frames/samples.
* MAY have gaps between frames/samples.
* MAY overlap with other objects. This means timestamps may be interleaved between objects.

# Mapping CMAF objects to MOQT Streams
This specification defines two methods for mapping CMAF objects to MOQT objects:

## CMAF Fragment to MOQT Group
A complete CMAF Fragment (see {{CMAF}} sect 6.6.1) into a single Object within each group. This results in there being a single GOP (Group of Pictures) in the media object and a single media object per Group.

## CMAF Chunk to MOQT Object
* Each CMAF chunk (see {{CMAF}} sect 6.6.5) in a separate MOQT Object. All MOQT Objects holding chunks from the same parent fragment MUST belong to the same MOQT Group. A new MOQT Group MUST be generated for each new CMAF Fragment.

# Switching sets

CMAF switching sets are a set of one or more CMAF tracks (3.2.1), where each track is an alternative encoding of the same source content and are constrained to enable seamless track switching (3.3.9). If such switching sets are to be transported over MOQT, irrespective of the mapping of CMAF Objects to MOQT Streams, then MOQT Group numbers MUST be media time-aligned between the MOQT tracks. Media time-aligned requires that the presentation time of the first media sample contained within the first MOQT Object of each MOQT Group is identical.

# Initialization headers
A CMAF header is sequence of CMAF constrained ISO BMFF boxes that do not reference any media samples (3.3.15), but are associated with a CMAF track (3.2.1) and necessary for the decoding of its CMAF fragments (3.1.1). The header for a given MOQT Track may be packaged in one of two ways:

## MOQT Tracks
As a MOQT Track. In this case the track MUST have only a single GROUP and a single OBJECT. The payload of the object MUST be the complete initialization header. The mapping of this initialization MOQT TRACK to the MOQT track which it initializes is defined by the streaming format.

# Content protection and encryption

The media object payloads MAY be encrypted. If the content is encrypted then Common Encryption {{CENC}} MUST be used. CMAF Track encruption MUST be applied following {{CENC}} Section 8.2. using either CENC with 'cbcs' mode (AES 128-bit keys in Cipher-block chaining mode and pattern encruption) or 'cenc' mode (AES 128-bit keys in Counter Mode).

Any license acquisition information used to acquire CMAF decryption key(s) MUST be signalled by the Streaming Format, and not in the CMAF header.


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
