---
layout: post
title: "RDP over Kerberos"
permalink: "rdpkerberos"
date: 25-05-2025
categories: Blog
---

If you are like me and prefer to use Kerberos moving around environments, there was for me at least, always a slight hurdle when wanting to RDP to various machines. I could get 'Pass The Hash' working using xfreerdp but using Kerberos was something I wasn't able to get right.

On top of that even when using an admin hash for a machine I would sometimes come across this alert preventing me from accessing it via PTH too.

<img src="{{ "/assets/img/accountrestrictions.jpg" | prepend: site.baseurl }}" />

This issue relates to the Windows feature known as 'Restricted Admin' which was introduced by Microsoft to prevent clear text passwords being cached in LSASS and therefore stolen by threat actors. The idea of this mode was to allow users that possess local administrative privileges on the Remote Desktop host to complete the authentication process without supplying their password in cleartext.

The catch though in this whack a mole situation, is that by doing this it then allowed threat actors to pass the hash and to also allow authentication using Kerberos. So this puts companies in a bind to decide whether to prioritise the chances of passwords being stolen from LSASS versus credentials being reused to get onto their machines.

Therefore when you read a lot of DFIR reports you actually see threat actors enabling this feature to allow them access!

### The inverse logic of RestrictedAdmin

When investigating this feature you inevitably get led to this particular registry key:

{% highlight powershell %}
HKLM:\System\CurrentControlSet\
Control\Lsa\DisableRestrictedAdmin
{% endhighlight %}

Given that this is a 'Disable' flag and that the 'RestrictedAdmin' mode despite its name is actually a good thing for us testers to move laterally with, we actually want this to be set to zero. Setting it to zero will enable 'RestrictedAdmin' mode on the box and allow us to get onto it via PTH or Kerberos.

### netxec commands

To play around with this flag we can use netxec given how easy it is to use. First we can check if the registry entry is present like so:

<img src="{{ "/assets/img/restrictedcheck.jpg" | prepend: site.baseurl }}" />

If it doesn't exist we can add the key and set it to zero:

<img src="{{ "/assets/img/restrictedadd.jpg" | prepend: site.baseurl }}" />

If the key did exist you can just set it to zero like so:

<img src="{{ "/assets/img/restrictedadd2.jpg" | prepend: site.baseurl }}" />

This will now allow us to PTH onto the box with xfreerdp:

{% highlight powershell %}
xfreerdp /size:1670x1430 +clipboard /cert:ignore /v:KENNEDY-DC.KENNEDY.LOCAL /u:dkennedy /pth:HASH /d:KENNEDY.LOCAL /dynamic-resolution
{% endhighlight %}

### Using Kerberos 

Now that we are able to pass the hash to get onto the box we want to get to the crux of this post and that is to be able to do so via Kerberos. First make sure you have the various Kerberos features installed for Linux like klist with this command:

{% highlight powershell %}
sudo apt install krb5-user
{% endhighlight %}

We then can get a TGT for our dkennedy user like so:

<img src="{{ "/assets/img/dkennedytgt.jpg" | prepend: site.baseurl }}" />

And now check for it in klist:

<img src="{{ "/assets/img/kerbklist.jpg" | prepend: site.baseurl }}" />

Now we need to set up a realm for our infrastructure. This involves editing the /etc/krb5.conf file which I will show below.
For my network my krb5.conf file has these changes:

<img src="{{ "/assets/img/krbinf1.jpg" | prepend: site.baseurl }}" />

<img src="{{ "/assets/img/krbinf2.jpg" | prepend: site.baseurl }}" />


The last piece of the jigsaw is to run your xfreerdp command with the key being to specify a blank password and use the restriced-admin flag as so:

{% highlight powershell %}
xfreerdp /size:1670x1430 +clipboard /cert:ignore /v:kennedy-dc.kennedy.local /u:dkennedy /p:'' /d:kennedy.local /dynamic-resolution /restricted-admin
{% endhighlight %}

After you have done this you can now connect to your target using RDP with your Keberos TGT. Hope you found this helpful. 












