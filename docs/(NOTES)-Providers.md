PRELIM DOC

Ext / Providers Mechanism

Borrowing some terms from official LE client:
  * https://letsencrypt.readthedocs.org/en/latest/contributing.html#writing-your-own-plugin

* ~~Authenticator~~ ChallengeHandler Providers
  * Challenge
    * Type (dns-01, http-01, tls-sni-01)
    * Challenge subclasses
      * DnsChallenge
      * HttpChallenge
      * TlsSniChallenge
  * ~~IAuthenticatorProvider~~ IChallengeHandlerProvider
    * bool IsSupported(Challenge)
    * IChallengeHandler GetHandler(Challenge)
  * ~~IAuthenticator~~ IChallengeHandler
    * ~~Authenticate(Challenge)~~ Handle(Challenge)

* Cert Installer Providers
  * ICertInstallerProvider
    * ICertInstaller GetInstaller()
  * ICertInstaller
    * Install(PkiManager, PrivateKey, Certificate)

* PKI Manager Providers
  * IPkiManagerProvider
  * IPkiManager
    * GeneratePrivateKey(...)
    * GenerateCsr(...)
    * ImportCertificate(...)
    * ExportArchive(...)
