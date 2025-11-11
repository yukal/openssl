# openssl
bash scripts to create ssl certificates using openssl and various ciphers

### Setup

1. Set your PKI directory path at:
- [ca_root.cnf](ca_root.cnf#L10)
- [ca_intr.cnf](ca_intr.cnf#L10)
- [make.ecdsa](make.ecdsa#L15)
- [make.eddsa](make.eddsa#L15)
- [make.rsa](make.rsa#L15)
- [ocsp](ocsp#L6) helper

2. Tie up domain names to IPs at your [/etc/hosts](etc-hosts)
```bash
127.0.0.1	server.loc			# front-end
127.0.0.1	dev.server.loc		# front-end (dev mode)
127.0.0.1	api.server.loc		# back-end
127.0.0.1	img.server.loc		# storing images (static files)
127.0.0.1	ocsp.server.loc		# OCSP resolver
```

### Deploy
Place the generated Root CA in a trusted location depending on your OS:

- [Fedora](https://docs.fedoraproject.org/en-US/quick-docs/using-shared-system-certificates/)
- [Ubuntu](https://documentation.ubuntu.com/server/how-to/security/install-a-root-ca-certificate-in-the-trust-store/)
- [Archlinux](https://wiki.archlinux.org/title/User:Grawity/Adding_a_trusted_CA_certificate)

Use `certutil` to place your certificates into a browser. See manual by:

- [Microsoft](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/certutil)
- [Ubuntu](https://manpages.ubuntu.com/manpages/jammy/man1/certutil.1.html)
- [Archlinux](https://wiki.archlinux.org/title/User:Grawity/Adding_a_trusted_CA_certificate)

```bash
PKI_DIR="your/PKI/dir"

# List of imported certificates in NSSDB
certutil -d "$HOME/.pki/nssdb" -L
certutil -d "$HOME/.pki/nssdb" -L -h all

# Check the certificate contents in NSSDB
certutil -L -d "$HOME/.pki/nssdb" -n "HomeWorks Root CA" -a
certutil -L -d "$HOME/.pki/nssdb" -n "HomeWorks Intermediate CA" -a

# Remove certificates
certutil -D -d "$HOME/.pki/nssdb" -n "HomeWorks Root CA"
certutil -D -d "$HOME/.pki/nssdb" -n "HomeWorks Intermediate CA"
certutil -D -d "$HOME/.pki/nssdb" -n "Client webmaster"

# Import certificates as a Trusted Certification Authority
certutil -A -t "C,C,C" -n "HomeWorks Root CA" \
  -i $PKI_DIR/root/certs/ca.cert.pem \
  -d "$HOME/.pki/nssdb"

certutil -A -t "C,C,C" -n "HomeWorks Intermediate CA" \
  -i $PKI_DIR/intermediate/certs/intermediate.cert.pem \
  -d "$HOME/.pki/nssdb"
```
