---
v: 3

title: >
  CBOR Diagnostic Notation: "cri" Application-oriented Extension
abbrev: >
  CBOR Diagnostic Notation: "cri" 
category: std
docname: draft-bormann-core-cri-diagnostic-notation-latest
submissiontype: IETF
consensus: true
area: WIT
workgroup: CoRE Working Group
keyword:
 - URI
 - CRI
 - Constrained Devices
 - URI Scheme
venue:
  mail: core@ietf.org
  github: cabo/draft-bormann-core-cri-diagnostic-notation


author:
  -
    ins: C. Bormann
    name: Carsten Bormann
    org: Universität Bremen TZI
    street: Postfach 330440
    city: Bremen
    code: D-28359
    country: Germany
    phone: +49-421-218-63921
    email: cabo@tzi.org


normative:
  STD94: cbor
  STD68: abnf
  # This actually includes RFC 7405.
#  STD63: utf8
  RFC3986: uri
  RFC3987: iri
#  BCP26: ianacons
  I-D.ietf-core-href: cri
  I-D.ietf-cbor-edn-literals: cdn
informative:

...

--- abstract

For the Concise Binary Object Representation (CBOR), a Concise
Diagnostic Notation (CDN) has been defined in
draft-ietf-cbor-edn-literals.
This provides an extension point for "App-Extension Identifiers"
registry in the "Concise Diagnostic Notation" registry group,
supporting text representations of CBOR tags and other structures to
enhance usability.

The present document specifies an app-extension and registers an
app-extension identifier for CBOR tag 99, Constrained Resource
Identifiers (CRI), as defined in RFC-to-be 9988.

[^status]

[^status]:
    (This cref will be removed by the RFC editor:)\\
    This document carries the specification of the cri''
    application-oriented prefix for CBOR diagnostic notation.
    This specification used to be in the CoRE WG document for CRIs,
    became part of the CBOR WG document for CBOR diagnostic notation
    when the CBOR WG document was delayed, and was then deleted from
    that document as it is outside the scope of the CBOR WG.
    So this document now goes back to the CoRE WG, as a "new"
    individual submission.

--- middle

# Introduction {#intro}

(To be derived from abstract, q.v.)
{{STD94}}

## Conventions and Definitions

{::boilerplate bcp14-tagged-bcp14}

 
Concise Resource Identifiers: The "cri" Extension {#cri}
=================================

The
app-extension identifier "`cri`" is used to notate
a CDN literal for a CRI reference as defined in {{-cri}}.

The input of the literal is a URI Reference as per {{-uri}} or an IRI
Reference as per {{-iri}}.

The value of the literal is a CRI reference that can be converted to
the text of the literal using the procedure of {{Section 6.1 of -cri}}.  <!-- {{cri-to-uri}}. -->
Note that there may be more than one CRI reference that can be
converted to the URI/IRI reference given; implementations are expected
to favor the simplest variant available and make non-surprising
choices otherwise.
In the all-uppercase variant of the app-prefix, the value is enclosed
in a tag number 99.

As an example, the CDN

~~~ cbor-diag
cri'https://example.com/bottarga/shaved'
CRI'https://example.com/bottarga/shaved'
~~~

is equivalent to

~~~ cbor-diag
[-4, ["example", "com"], ["bottarga", "shaved"]]
99([-4, ["example", "com"], ["bottarga", "shaved"]])
~~~

See {{cri-grammar}} for an ABNF {{STD68}} definition for the content of `cri` literals.


 
## cri: ABNF Definition of URI Representation of a CRI {#cri-grammar}

It can be expected that implementations of the app-extension
identifier "`cri`" will make use of platform-provided URI
implementations, which will include a URI parser.

In case such a URI parser is not available or inconvenient to
integrate,
a grammar of the content of `cri` literals is provided by the
ABNF for `URI-reference` in {{Section 4.1 of RFC3986@-uri}} with certain
re-arrangements taken from {{Section 5.2.4 of -cdn}};
these are reproduced in {{abnf-grammar-cri}}.
If the content is not ASCII only (i.e., for IRIs), first apply
{{Section 3.1 of RFC3987}} and apply this grammar to the result.

~~~ abnf
app-string-cri = URI-reference
; ABNF from RFC 3986:

URI           = scheme ":" hier-part [ "?" query ] [ "#" fragment ]

hier-part     = "//" authority path-abempty
                 / path-absolute
                 / path-rootless
                 / path-empty

URI-reference = URI / relative-ref

absolute-URI  = scheme ":" hier-part [ "?" query ]

relative-ref  = relative-part [ "?" query ] [ "#" fragment ]

relative-part = "//" authority path-abempty
                 / path-absolute
                 / path-noscheme
                 / path-empty

scheme        = ALPHA *( ALPHA / DIGIT / "+" / "-" / "." )

authority     = [ userinfo "@" ] host [ ":" port ]
userinfo      = *( unreserved / pct-encoded / sub-delims / ":" )
host          = IP-literal / IPv4address / reg-name
port          = *DIGIT

IP-literal    = "[" ( IPv6address / IPvFuture  ) "]"

IPvFuture     = "v" 1*HEXDIG "." 1*( unreserved / sub-delims / ":" )

; Use IPv6address, h16, ls32, IPv4adress, dec-octet as re-arranged
; for PEG Compatibility in Figure 6 of [RFC XXXX]:

IPv6address   =                            6( h16 ":" ) ls32
              /                       "::" 5( h16 ":" ) ls32
              / [ h16               ] "::" 4( h16 ":" ) ls32
              / [ h16 *1( ":" h16 ) ] "::" 3( h16 ":" ) ls32
              / [ h16 *2( ":" h16 ) ] "::" 2( h16 ":" ) ls32
              / [ h16 *3( ":" h16 ) ] "::"    h16 ":"   ls32
              / [ h16 *4( ":" h16 ) ] "::"              ls32
              / [ h16 *5( ":" h16 ) ] "::"              h16
              / [ h16 *6( ":" h16 ) ] "::"

h16           = 1*4HEXDIG
ls32          = ( h16 ":" h16 ) / IPv4address
IPv4address   = dec-octet "." dec-octet "." dec-octet "." dec-octet
dec-octet     = "25" %x30-35         ; 250-255
              / "2" %x30-34 DIGIT    ; 200-249
              / "1" 2DIGIT           ; 100-199
              / %x31-39 DIGIT        ; 10-99
              / DIGIT                ; 0-9

reg-name      = *( unreserved / pct-encoded / sub-delims )

path          = path-abempty    ; begins with "/" or is empty
                 / path-absolute   ; begins with "/" but not "//"
                 / path-noscheme   ; begins with a non-colon segment
                 / path-rootless   ; begins with a segment
                 / path-empty      ; zero characters

path-abempty  = *( "/" segment )
path-absolute = "/" [ segment-nz *( "/" segment ) ]
path-noscheme = segment-nz-nc *( "/" segment )
path-rootless = segment-nz *( "/" segment )
path-empty    = 0<pchar>

segment       = *pchar
segment-nz    = 1*pchar
segment-nz-nc = 1*( unreserved / pct-encoded / sub-delims / "@" )
                 ; non-zero-length segment without any colon ":"

pchar         = unreserved / pct-encoded / sub-delims / ":" / "@"

query         = *( pchar / "/" / "?" )

fragment      = *( pchar / "/" / "?" )

pct-encoded   = "%" HEXDIG HEXDIG

unreserved    = ALPHA / DIGIT / "-" / "." / "_" / "~"
reserved      = gen-delims / sub-delims
gen-delims    = ":" / "/" / "?" / "#" / "[" / "]" / "@"
sub-delims    = "!" / "$" / "&" / "'" / "(" / ")"
                 / "*" / "+" / "," / ";" / "="
~~~
{: #abnf-grammar-cri sourcecode-name="cdn-ext-cri.abnf"
title="ABNF Definition of URI Representation of a CRI"
}


# Security Considerations {#security}

TODO Security


IANA Considerations {#sec-iana}
===================

[^to-be-removed]

[^to-be-removed]: RFC Editor: please replace RFC-XXXX with the RFC
    number of this RFC, \[IANA.concise-diagnostic-notation] with a
    reference to the new registry group, and remove this note.


## Concise Diagnostic Notation App-extension Identifiers Registry {#appext-iana}

IANA is requested to register the "cri" app-extension identifier as
per {{tab-iana}} in the "App-Extension Identifiers" registry in the
"Concise Diagnostic Notation" registry group
\[IANA.concise-diagnostic-notation], with the Change Controller
"IETF".

| app-extension identifier | Description                     | Reference          |
|--------------------------|---------------------------------|--------------------|
| cri                      | Constrained Resource Identifier | RFC-XXXX, {{-cri}} |
 {: #tab-iana title='app-extension Identifier "cri"'}


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
