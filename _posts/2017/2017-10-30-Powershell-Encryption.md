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

This created a key-pair in our local cert store, but it also created a file:

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 DocumentEncryption.cer %}

So, now we can start encrypting things!

## Encrypt all the things

### Protect-CMSMessage & Unprotect-CMSMessage

### Encryption

There's two CMDlets that we're going to be using to use the certificates and protect our data from those that would do us harm. (sorry, had to add some drama...) Those two commands are 'Protect-CMSMessage' and 'Unprotect-CMSMessage.' They are part of the [Microsoft.Powershell.Security](https://technet.microsoft.com/en-us/library/hh847877.aspx) module which is part of [Powershell 5.0 and 5.1](https://technet.microsoft.com/en-us/library/hh847877.aspx)\.

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 output1.txt %}

The first thing we want to do is encrypt our secure data. To do this we use the aptly named [Protect-CMSMessage](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/Protect-CmsMessage?view=powershell-5.1)\.

> One thing to note: You can only encrypt text with these commands, you can't encrypt objects or binary files. I'll try to cover how to convert binary file information into base64 encoding so that you can encrypt it, but it's a processor intensive task that is not very useful. In order to capture an object, you'll need to pull it out as a CLIXML file or JSON and then convert it back into an object and cast it as the appropriate class type. Most of the time, you're going to be storing passwords or API keys and other simple sensitive data. For this example, we're going to concentrate on strings only.
{: title="Please Note"}

Here's how we encrypt our secure data:

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 EncryptSomeStuff.ps1 %}

And this is our output...

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 output2.txt %}

And the file we created...

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 password.cms %}

So, that means nothing if we can't use it.

Let's use it.

### Decryption

Since our key is stored in the user store, we can use some shortcuts. But, I've found when it's in the machine store, we have to pull the private key out. I'm still working on that, just in case I'm making a mistake, I'll update the post accordingly.

So, this is how we're going to get the password and create a pscredential object out of it...

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 CreateAPSCredential.ps1 %}

And this is out output from that...

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 output3.txt %}

That's about all there is to that.

## But, wait, there's more

### Envelopes

Our CMS has an envelope (if you read the RFC, you'd know this already... lol) that we can access with [Get-CMSMessage](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/get-cmsmessage?view=powershell-5.1)\.

We can do that like this...

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 ShowTheEnvelope.ps1 %}

And that outputs...

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 output4.txt %}

### Export the Private Key

Microsoft's given us a few other tools to work with the certificate store. One thing we want to do is export the key-pair including the Private Key into a password protected file that we can transport to servers or maybe store incase our machine needs to be rebuilt.

Looking in the PKI module, we have [Export-PfxCertificate](https://docs.microsoft.com/en-us/powershell/module/pkiclient/Export-PfxCertificate?view=win10-ps)\.

{% gist mockmyberet/7dd93fa7bfeac98ef6dea96a9a5f44a5 output5.txt %}

### Import the Private Key

And we have the [Import-PfxCertificate](https://docs.microsoft.com/en-us/powershell/module/pkiclient/import-pfxcertificate?view=win10-ps)\.

## More to come

*[CMS]: Cryptographic Message Syntax
*[JSON]: JavaScript Object Notation
