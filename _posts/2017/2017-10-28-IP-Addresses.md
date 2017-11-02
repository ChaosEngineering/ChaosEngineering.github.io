---
layout: post
title: IP Addresses and Powershell
author: Tommy
date: '2017-10-28 4:17'
excerpt: 'Hey, I was thinking about a whole list of IPs...'
feature: assets/img/systems.jpeg
comments: true
tags:
    - thoughts
    - code
---
So, I wanted a way to iterate through a series of IP addresses given any two addresses. For example, if I wanted all the addresses between 10.0.0.1 and 10.0.0.20\. I wanted to do this in the most PowerShell way possible... so I started with an IP address.

```powershell
PS scripts:\> [ipaddress]"10.0.0.1"

Address            : 16777226
AddressFamily      : InterNetwork
ScopeId            :
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : 10.0.0.1
```

Well, that looks promising. It looks like they give you an Address property to work with. Let's check another IP...

```powershell
PS scripts:\> [ipaddress]"10.0.0.0"

Address            : 10
AddressFamily      : InterNetwork
ScopeId            :
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : 10.0.0.0
```

Wait, that doesn't make sense, how is this address '10?'

Good question, the answer is, it is a result of a LittleEndian calculation. Doing some research, Microsoft says that the property is obsolete and should not be used. (See: [http://msdn.microsoft.com/en-us/library/system.net.ipaddress.address.aspx](http://msdn.microsoft.com/en-us/library/system.net.ipaddress.address.aspx))

So, I did some research about ranges of IP addresses in PowerShell and found some scripts to build the address list manually, but that's not what we want, we want the system to do it, I know there's a way.

I then did some research into sorting IP addresses using PowerShell, since it makes sense that someone has wanted to do this and the best way to sort IP addresses is to use their decimal equivalent, someone must have tried to convert an address into a decimal.

Then I found it, a great blog article written several years ago about creating a type property and adding it to the ipaddress class. (See: [http://rkeithhill.wordpress.com/2007/06/23/sorting-ipaddresses-the-powershell-way/](http://rkeithhill.wordpress.com/2007/06/23/sorting-ipaddresses-the-powershell-way/))

{% gist mockmyberet/4b873f87c2f2f7bdc08f %}

In his article, he documents that you need to create a BigEndian address property and sort on that. So, we create a .ps1xml file, containing the following: (For more about format.ps1xml files, see: [http://technet.microsoft.com/en-us/library/hh847831.aspx](http://technet.microsoft.com/en-us/library/hh847831.aspx))

Load this file by using Update-TypeData and you'll get...

```powershell
PS scripts:\> [ipaddress]"10.0.0.0"

Address            : 10
AddressFamily      : InterNetwork
ScopeId            :
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : 10.0.0.0
BigEndianAddress   : 167772160
```

So, now we can do the following:

```powershell
PS scripts:\> ([ipaddress]"10.0.0.1").BigEndianAddress .. ([ipaddress]"10.0.0.20").BigEndianAddress|%{([ipaddress]::Parse($_)).IPAddressToString}
10.0.0.1
10.0.0.2
10.0.0.3
10.0.0.4
10.0.0.5
10.0.0.6
10.0.0.7
10.0.0.8
10.0.0.9
10.0.0.10
10.0.0.11
10.0.0.12
10.0.0.13
10.0.0.14
10.0.0.15
10.0.0.16
10.0.0.17
10.0.0.18
10.0.0.19
10.0.0.20
```

It works on any range:

```powershell
PS scripts:\> $range = ([ipaddress]"10.0.0.1").BigEndianAddress .. ([ipaddress]"10.0.10.1").BigEndianAddress|%{([ipaddress]::Parse($_)).IPAddressToString}

PS scripts:\> $range | select -First 10
10.0.0.1
10.0.0.2
10.0.0.3
10.0.0.4
10.0.0.5
10.0.0.6
10.0.0.7
10.0.0.8
10.0.0.9
10.0.0.10

PS scripts:\> $range | select -last 10
10.0.9.248
10.0.9.249
10.0.9.250
10.0.9.251
10.0.9.252
10.0.9.253
10.0.9.254
10.0.9.255
10.0.10.0
10.0.10.1

PS scripts:\> $range.count
2561
```

And that's about it.

$$
\large x+\frac{1}{x} = 5, \qquad x^2 + \frac{1}{x^2} = \, ?
$$
