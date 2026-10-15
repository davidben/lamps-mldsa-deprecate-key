---
title: "Deprecating Expanded ML-DSA Private Key Encodings"
abbrev: "Deprecating Expanded ML-DSA Private Key Encodings"
category: std
updates: 9881

docname: draft-rosomakho-lamps-mldsa-seed-only-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Security"
workgroup: "Limited Additional Mechanisms for PKIX and SMIME"
keyword:
 - mldsa
 - expanded
 - deprecate
venue:
  group: "Limited Additional Mechanisms for PKIX and SMIME"
  type: "Working Group"
  mail: "spasm@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/spasm/"
  github: "yaroslavros/lamps-mldsa-deprecate-key"
  latest: "https://yaroslavros.github.io/lamps-mldsa-deprecate-key/draft-rosomakho-lamps-mldsa-seed-only.html"

author:
 -
    fullname: Yaroslav Rosomakho
    organization: Zscaler
    email: yrosomakho@zscaler.com

normative:

informative:

...

--- abstract

This document updates RFC 9881 to require the `seed` representation when
encoding Module-Lattice-Based Digital Signature Algorithm (ML-DSA) private
keys for interchange. It deprecates the `expandedKey` and `both`
representations, while permitting their continued support for legacy
imports. These changes simplify implementation and improve
interoperability by aligning the private-key representation with that
used by JSON Object Signing and Encryption (JOSE) and CBOR Object Signing
and Encryption (COSE). Internal private-key representations are unaffected.


--- middle

# Introduction {#intro}

{{!X509-MLDSA=RFC9881}} defines three representations for Module-Lattice-Based Digital
Signature Algorithm (ML-DSA) private keys: a 32-octet seed, an expanded
private key, and a combination of the seed and expanded private key. The
seed deterministically yields both the expanded private key and the public
key. Although {{Section 8.1 of X509-MLDSA}} recommends the seed
representation, it permits all three representations for private-key
interchange.

Multiple representations complicate interoperability between
implementations that support different subsets of these formats. An
implementation that supports only seeds cannot import an expanded private
key without its seed. The combined representation also introduces a
consistency check between the seed and expanded private key, which can
require additional implementation support solely for importing that
representation.

{{?JOSE-COSE-MLDSA=RFC9964}} specifies only the seed representation for ML-DSA private keys
in JSON Object Signing and Encryption (JOSE) and CBOR Object Signing and
Encryption (COSE). Using the seed representation consistently across these
specifications simplifies private-key interchange and avoids the need to
support multiple representations and their associated validation logic.

This document updates {{X509-MLDSA}} to require the `seed` representation when
encoding ML-DSA private keys for interchange and deprecates the `expandedKey`
and `both` representations. Support for importing the deprecated
representations remains optional for legacy compatibility. This document
does not restrict internal private-key representations or change public
keys, signatures, or their encodings.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Updates to ML-DSA Private Key Encoding {#updates}

This section updates the private-key encoding requirements in
{{Section 6 of X509-MLDSA}} and the format recommendations in
{{Section 8.1 of X509-MLDSA}}. These updates apply to ML-DSA-44, ML-DSA-65,
and ML-DSA-87.

Implementations that encode or decode ML-DSA private keys for interchange
using the structures defined in {{X509-MLDSA}} MUST support the `seed`
representation. When encoding such a private key, implementations MUST
use the `seed` alternative and MUST NOT use the `expandedKey` or `both`
alternatives. This requirement replaces the permission to encode any of
the three alternatives in {{Section 6 of X509-MLDSA}} and
{{Section 8.1 of X509-MLDSA}}.

The `seed` encoding remains unchanged: the `privateKey` field of
OneAsymmetricKey contains the DER encoding of the implicitly tagged
`seed` alternative, consisting of the octets 0x80 and 0x20 followed by the
32-octet seed. The algorithm identifiers and the requirement that their
parameters be absent remain unchanged. No new ASN.1 types or algorithm
identifiers are defined.

The `expandedKey` and `both` alternatives are deprecated. Implementations
MAY support decoding these alternatives for legacy compatibility, but
such support is not required for conformance to this specification.


# Compatibility and Migration Considerations {#migration}

Existing private keys can be re-encoded in the `seed` representation when
the original seed is available, including from the `both` representation.
This conversion does not require replacing the key pair or reissuing
certificates. Operators should ensure that seeds are retained for keys
that may need to be exported or restored into another implementation.

As noted in {{Section 8.1 of X509-MLDSA}}, a discarded seed cannot be
recovered from the expanded private key. If no copy of the seed is
available, migration to an implementation that accepts only seeds requires
generating a new key pair and replacing any associated certificates or
other public-key bindings. Existing keys can continue to be used by
implementations that support their stored representation.


# Security Considerations {#security}

The security considerations of {{X509-MLDSA}} continue to apply. The seed
is sufficient to reconstruct the private key and generate signatures, so
it requires the same protection as the expanded private key, including
when retained in backups or transferred between implementations.


# IANA Considerations {#iana}

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
