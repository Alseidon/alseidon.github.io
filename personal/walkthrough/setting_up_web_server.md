+++
title = "Setting up a web server with Apache"

hasmath = false
date = Date("07/01/2026", "dd/mm/yyyy")

tags = ["walkthrough", "web", ]
+++

## Starters
I have a Raspberry Pi on which I have several ongoing projects I wanted to expose to the Web.

## Registering a DNS zone
- With Infomaniak
- From the domain *.alexandre.prieur.eu to the Wi-Fi router
- Two certificates: A (for IPv4) and AAAA (for IPv6)

## Forwarding to the Raspberry Pi
- Wi-Fi router (exact steps depend on the brand and model)
- Forward public IP ports to Raspberry Pi ports
  - I chose non-standard public ports to avoid getting bothered by automatic scripts
  - I set up a static local IP for the Raspberry Pi

### Dynamic DNS with ddclient
As my public IP isn't static, I needed to set up dynamic DNS to inform the registrar (Infomaniak) if my IP changed. Following [this guide](https://www.infomaniak.com/fr/support/faq/40/configurer-ddclient-avec-dyndns-infomaniak) was quite easy.

## On the Raspberry Pi: the Apache server

### A simple HTTP page with Apache
- 

### Enabling HTTPS
- https://doc.ubuntu-fr.org/tutoriel/securiser_apache2_avec_ssl
- https://certbot.eff.org/instructions?ws=apache&os=pip&tab=wildcard
- https://github.com/Infomaniak/certbot-dns-infomaniak

Command:
```bash
certbot certonly \
    --authenticator dns-infomaniak \
    --server https://acme-v02.api.letsencrypt.org/directory \
    --agree-tos --rsa-key-size 4096 -d *.alexandre.prieur.eu \
    --dns-infomaniak-credentials /path/to/token.secret
```