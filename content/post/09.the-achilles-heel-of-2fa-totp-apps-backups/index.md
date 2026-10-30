---
title: 'The Achilles Heel of 2FA TOTP Apps : Backups'
date: '2023-02-09'
---

The authors of ["Security and Privacy Failures in Popular 2FA Apps"](https://www.usenix.org/system/files/sec23summer_198-gilsenan-prepub.pdf) describe security and privacy shortcomings in popular TOTP Android apps. 

TOTP(time-based one time password) is one of the most common 2FA authentication methods and seen as a replacement for SMS based 2FA. TOTP works by setting up a shared secret between server and client(you). The TOTP app manages these secrets. If this secret is lost there is no way to get it back. This happens quite often: users delete the app, phone gets stolen, etc. That is why most TOTP apps allow automatic backups. The work of Gilsenan et al. shows that these backup mechanisms are usually lacking. 

It is a very interesting read, especially because TOTP is the de facto choice for 2FA. If TOTP secrets get leaked it peals away the second factor layer. Even apps made by wellknown companies make trivial mistakes like sending both encrypted backup and the key to their server, allowing unencrypted backups, or encryption flaws like static passwords/salts. They published their results in the respected USENIX symposium. For more info, also visit their [github page](https://github.com/blues-lab/totp-app-analysis-public).