---
title: 'Confidential Linux Server Reports using PGP and mailx'
date: '2023-01-26'
---

To monitor some servers I regularly send automatic reports from these machines to my email address. However, by default the mail is not encrypted. Even if the mail is encrypted, mail providers can still read it. To prevent this you can use PGP to achieve end-to-end encryption. I will show a simple trick how to send automatic secure reports from your server using PGP. To achieve this you have to do a few things:

# Generate a PGP keypair(if you don't have one)
If you don't already have a PGP keypair, you need to generate one.
The best way to generate a PGP key pair is to do it [yourself](https://www.encryptionconsulting.com/how-to-generate-pgp-key-pair/).
To install gpg and generate a keypair:
```console
apt install gpg
gpg --full-generate-key
```
Just fill in a valid email address and setup a safe password for this keypair. Optionally, you can send your public key to a key server.
Protect your private key! So, do not upload you private key to a keyserver. 

# Import key (if you already have one)
If you already have a keypair, you need to import it. The server needs to know which key to use. Importing a key is easy, just use the following command:
```console
gpg --search [EMAIL]
```
This just looks for the public key in a keyserver. You can specify a specifc keyserver with the --keyserver flag. For example, "--keyserver pgp.mit.edu".
Alternatively you can import a public key with "gpg --import < [KEY FILE]".

# Trust the PGP key
If the key is not trusted it cannot be used. Trusting a key is simple:
```console
gpg --edit-key [EMAIL]
gpg> trust
  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu

Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y
```
Now the key is trusted and can be used.

# Sending email
To send the output of a command, just use the following command:
```bash
[COMMAND] | gpg --armor --batch --armor -e -r [TO] | mailx -r [FROM] -s "[SUBJECT]" [TO]
```
Make sure that TO and FROM are valid email addresses and that TO is the email address you have set up a PGP key for.
The --armor flag makes sure that the output is proper ASCII (otherwise characters might get mangled). The --batch flag makes sure that the command is not interactive, which is useful if this command is executed automatically without user supervision. -e stands for encryption and -r for recipient. For people not familiar with Linux pipes "|", it directs the output from the left command to the input of the right command. For example,
```bash
echo "hello"  | gpg --armor --batch --armor -e -r [TO] | mailx -r [FROM] -s "[SUBJECT]" [TO]
```
will send the string "hello" to the gpg command, that encrypts it with the public key of TO. The encrypted text is then mailed used mailx.

# Conclusion
The way I use this setup is that on my Linux servers I have configured some cronjobs. These cronjobs are able to send a report to my mail address automatically given that all commands are non-interactive. The mail will be delivered in a secure way because it is encrypted using PGP. I use the mailx client, but any program that can send mail would do.
