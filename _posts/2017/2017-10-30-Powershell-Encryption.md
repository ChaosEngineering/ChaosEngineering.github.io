---
layout: post
title: Powershell 5.0 and CMS Encryption
author: Tommy Becker
date: 2017-10-30 12:49
excerpt: I've been trying to research CMS encryption in Powershell 5.0 and have found very little out there.
feature: assets/img/chips.jpeg
---
# CMS Encryption and Powershell 5.0

## What is CMS

Cryptographic Message Syntax (or CMS) is defined by [RFC5652](https://tools.ietf.org/html/rfc5652), it defines a syntax to digitally sign, digest, authenticate, or encrypt arbitrary message content. That's a whole lot of words to use to say that, with an number of certificate-based key management systems, we can secure data for storage and transport across insecure mediums, such as the internet.

What we're going to do is create a Public and Private key pair and use them to encrypt and decrypt messages, respectively. For those unfamiliar with this method of key sharing, there are two keys involved. First there is the Public Key that is used to encrypt the data. This key is intended to be able to be shared freely so that messages can be encrypted by anyone. Then, there is the Private Key that is kept secret that is used to decrypt any of the messages that were encrypted by the respective Public Key.

The Public Key certificate will be in a file that we designate with a .cer extension. The Private Key certificate (along with the Public Key) will be installed in our certificate store on the local system. We will mark it as exportable, so that we can export the key pair as a password secured .pfx file for transport to another system or for backing it up for future recovery or use.

Alright, let's get to it.

## Create a new certificate key pair

Following instructions from Microsoft, [here](https://docs.microsoft.com/en-us/powershell/wmf/5.0/audit_cms), create a file as follows.

Make sure to change the subject to what you feel is appropriate, this is how we will refer to it in our Powershell commands.

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 DocumentEncryption.inf %}

Save the file as something like DocumentEncryption.inf, this is the information we will pass in the next command that will generate the key pair, install it to our User Key store and save out a Public Key file that we can share and use to encrypt our messages.

So, run the following to do so:

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 Generate.cmd %}

So, now we can start encrypting things!

## Encrypt all the things

### Protect-CMSMessage & Unprotect-CMSMessage

There's two CMDlets that we're going to be using to use the certificates and protect our data from those that would do us harm. (sorry, had to add some drama...) Those two commands are 'Protect-CMSMessage' and 'Unprotect-CMSMessage.' They are part of the [Microsoft.Powershell.Security](https://technet.microsoft.com/en-us/library/hh847877.aspx) module which is part of [Powershell 5.0 and 5.1](https://technet.microsoft.com/en-us/library/hh847877.aspx)\.

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 output1.txt %}

The first thing we want to do is encrypt our secure data. To do this we use the aptly named [Protect-CMSMessage](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/Protect-CmsMessage?view=powershell-5.1)\.
