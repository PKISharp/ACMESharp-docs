[ACMESharp Provider Extensions](../) > Challenge Handlers

# Provider Extensions for Challenge Handler

Challenge Handlers are used by ACMESharp to fulfill *Challenges* issued by
an ACME CA server to prove ownership and control of a DNS *Identifier*
(domain name) before the server can generate a signed certificate for that
Identifier.

The ACME protocol defines several standard *Identifier Validation* types
which each have their own protocol and set of procedures to satisfy the
Challenge.  Each of these Validations is also versioned in order to support
evolution of these protocols and procedures.  Examples of some of these
Validations are `dns-01` and `http-01`.

A Provider Extension for Challenge Handling defines which Validation types
it supports and optionally if it supports *cleaning up* after itself --
this may entail removing any residual artifacts or reverting state that
it may have produced in order to complete the Challenge.

* [`manual`](manual.md)
* [`aws-route53`](aws-route53.md)
* [`aws-s3`](aws-s3.md)
* [`CloudFlare`](CloudFlare.md)
* [`iis`](iis.md)
* [`msdns`](msdns.md)
