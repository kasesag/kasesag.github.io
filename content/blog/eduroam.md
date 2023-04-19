+++
title = "Connecting to eduroam Wifi on modern Linux distributions"
date = 2023-04-18T12:31:45+02:00
categories = ['linux']
description = "Guide explaining how to configure eduroam connection on newer Linux systems."
# slug = ""
tags = []
draft = false
+++

If you're a university student and a Linux user, you already know what's eduroam and have experienced the pain when it came to the configuration. I encountered such a problem under Fedora Linux, and even official python scripts didn't help me at all.

Since most guides are out-of-date, I decided to write one for people who may encounter the same issue which I had. Here I will be using Fedora 37 (KDE) to demonstrate everything. Instructions given below may also apply to any other distributions which utilise **wpa_supplicant**, **NetworkManager** and **OpenSSL**.

## Prerequisites

Firstly, verify that you have administration privileges on your system before using this guide. You should also note that **It is generally not recommended to enable older cryptographic algorithms unless you really need to**. Use the guide reasonably.

## Solution 1 (Fedora related)

On Fedora, you simply need to execute this command with root privileges:

```update-crypto-policies --set DEFAULT:FEDORA32```

or If It fails, change 'DEFAULT:FEDORA32' over to 'LEGACY'. You'll then get:

```update-crypto-policies --set LEGACY```

After successful execution reboot your system.

## Solution 2

Since the utility **update-crypto-policies** is exclusive to Fedora-based distros, the solution will differ for other Linux distributions using OpenSSL.

Find and open text file:

```/etc/pki/tls/openssl.cnf```

Scroll to the line which mentions *crypto_policy*
```
[ crypto_policy ]

.include = /etc/crypto-policies/back-ends/opensslcnf.config
```

Under the section *crypto_policy*, add the following line, which will enable older authentication methods.
```
Options = UnsafeLegacyRenegotiation
```
The result should look like this:
```
[ crypto_policy ]

Options = UnsafeLegacyRenegotiation
.include = /etc/crypto-policies/back-ends/opensslcnf.config
```
Save the file and reboot your system.

## Connection properties for NetworkManager
Now after you've done the policy update, you should be now able to establish the connection with eduroam wifi. This may not be always blindingly obvious, but you need to set some properties when you want to connect with the network.

### Properties
- **Security:** WPA/WPA2 Enterprise
- **Authentication:** Protected EAP (PEAP)
- **Domain:** (your uni domain, you may also leave this blank)

Check the box 'No CA certificate required' when It's present (under GNOME).

- **PEAP version:** Automatic
- **Inner authentication:** MSCHAPv2
- **Username:** (provide your uni email address)
- **Password:** (provide your password)

## Sources:
- https://fedoraproject.org/wiki/Changes/StrongCryptoSettings2
- https://discussion.fedoraproject.org/t/cannot-connect-to-wpa2-enterprise-university-wifi-eduroam-on-fedora-36
