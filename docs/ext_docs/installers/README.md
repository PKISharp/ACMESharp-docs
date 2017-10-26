[ACMESharp Provider Extensions](../) > Installers

# Provider Extensions for Installers

Installers are used by ACMESharp to install PKI certificates obtained from
an ACME CA server.  These installers can install the certificate into any
server (such as a Web server, mail server or proxy server) or into a
service (such as a cloud-hosted Web site, CDN service or load balancer).

Installers may optionally support uninstallation as well.

Certificate installation is not technically a part of the ACME protocol as
it takes place after an ACME certificate is issued and exchanged, however
it is a natural extension of the entire process, and therefore *completes
the story* of automated certificate management and exchange.

* [`aws-iam`](aws-iam.md)
* [`aws-elb`](aws-elb.md)
* [`win-cert`](win-cert.md)
* [`iis`](iis.md)
