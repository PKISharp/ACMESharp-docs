# Background

In order to help understand the details of ACMESharp, it is important to first understand some basic concepts of the ACME protocol.  ACME is used to support automated certificate request and issuance from a Certificate Authority.  An ACME *Client* (such as ACMESharp) interacts with an ACME *Server* through a series of message exchanges. The typical, high-level flow is as follows:
1. Client creates a new *Account* with Server
2. Client validates ownership of a DNS *Identifier* (domain name) to the Server
3. Client generates a certificate signing request (CSR) for a *validated* Identifier
4. Client requests a certificate by submitting the CSR to the Server
5. Client retrieves signed certificate from Server and installs it into one or more servers or services
6. Client will periodically renew the certificate by repeating steps 2-5
7. Optionally, Client may choose to revoke a certificate with the Server

# Extension Provider Model

In the ACME protocol flow described above there are many places where the steps can vary greatly in how processing can be handled, both within the ACME protocol itself as well as external integrations and dependencies.  For example, the ACME protocol defines several mechanisms for validating that a Client is the owner of a DNS Identifier and allows for one or more of them to be used as prescribed by the Server.  The protocol explicitly allows for mechanisms to evolve over time and for new mechanisms to be supported.  Similarly, once a certificate is issued from Server to Client, the Client may wish to install that certificate into various different server applications or services.

To address the need to accommodate all these areas of variations, and allow for an open-ended system that can evolve along with the ACME protocol and future integrations, ACMESharp is built on a core extension mechanism that allows feature-specific *Providers* to handle these variations.

The *Extension Provider Model* follows this general pattern:
  * Service Interface - high-level, well-defined set of functions or features for a specific area of functionality
  * Service Provider - factory or generator of concrete implementations of a Service Interface
  * Extension Manager - central management for Provider discovery and usage for a particular Service Interface

## Extension Types

Within ACMESharp there are several *areas of functionality* that leverage the Extension Provider Model to offer points of extensibility:
  * **Challenge Decoder** - decodes an ACME protocol challenge to provide DNS Identifier ownership
  * **Challenge Handler** - handles satisfying an ACME protocol challenge based on decoded elements
  * **PKI Tool** - used to handle all the Client-side operations of X509 certificate management, including private key generation, certificate signing request (CSR) generation and certificate format transformation
  * **Installer** - supports the installation (and optional removal) of certificates from a server or service
  * **Vault Storage** - supports the secure persistence of durable artifacts generated and received in the ACME protocol flow

For more info, please see the [reference documentation](http://pkisharp.github.io/ACMESharp-docs/ext_docs) for standard Extension Providers.
