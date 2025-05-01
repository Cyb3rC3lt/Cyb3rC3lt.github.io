---
layout: post
title: "Sliver Beacon Object File"
permalink: "sliverbof"
date: 18-10-2023
categories: Blog
---

I was recently playing around with my favourite C2 framework which is [Sliver](https://github.com/BishopFox/sliver) from Bishop Fox. For those of you who that haven't used it, it runs completely in the terminal unlike for example, Cobalt Strike or Havoc.

Whilst using it,I noticed that a lot of times when I ran certain commands that Microsoft Defender would kill my connection between my implant and C2 server. This mainly occured when running commands like 'execute' and more specifically when I wanted to run 'execute -o klist' to view my cached Kerberos tickets.

The reason for this is the infamous 'fork and run' IOC (indicator of compromise) that Defender picks up on. Essentially when certain commands are run via a lot of C2's they create a sacrifical process (the fork) to run the command. When Defender sees this forking it flags it as supicious and kills the connection.

To avoid this, 'beacon object files' (BOF's) were created, which are small C programs that run within the current process without forking and are a lot more OPSEC friendly. These BOF's are normally not meant to be long running programs but used to quickly get some information back so keep that in mind when designing them.

So to make a long story short, I noticed that the 'klist' command wasn't supported as a BOF so I decided to port a klist BOF from Cobalt Strike to Sliver.


### Scripts

Before I go any further, I want to say that I am standing on the shoulders of giants here and that I didn't build this from scratch but just refactored the Cobalt Strike BOF created by OutflankNL to now work with Sliver.

To get BOF's to work with Sliver you ideally want 3 files:

- An x86 object file for 32 bit systems.
- An x64 object file for 64 bit systems.
- An extension.json file shown below to tell Sliver where the files are located and other config information.

**extension.json:**
{% highlight powershell %}
{
    "name": "klist",
    "version": "1.0.0",
    "command_name": "klist",
    "extension_author": "Cyb3rC3lt",
    "original_author": "OutflankNl",
    "help": "Displays a list of currently cached Kerberos tickets.",
    "long_help": "",
    "depends_on": "coff-loader",
    "entrypoint": "go",
    "files": [
        {
            "os": "windows",
            "arch": "amd64",
            "path": "klist.x64.o"
        },
        {
            "os": "windows",
            "arch": "386",
            "path": "klist.x86.o"
        }
    ],
    "arguments": [
        {
            "name": "purge",
            "desc": "Purge the cached Kerberos tickets.",
            "type": "wstring",
            "optional": true
        }
    ]
}
{% endhighlight %}

### Install The Release

The quickest way to add these 3 files to Sliver is as follows.

1. Download the zip file from my releases [here](https://github.com/Cyb3rC3lt/SliveryArmory/releases/tag/v1.0.0)
2. Extract it to a folder on your machine named klist for argument sake.
3. Within Sliver load the folder you extracted with this command:
{% highlight powershell %}
extensions install /home/david/klist
{% endhighlight %}
4. Then load the extension into Sliver as follows:
{% highlight powershell %}
extensions load /home/david/.sliver-client/extensions/klist
{% endhighlight %}

### Install From Source

1. Make sure that Mingw-w64 (including mingw-w64-binutils) has been installed.
2. Download the source folder above.
3. Within that folder execute "make" to compile the object files.
4. Now you have the object files like they appear in the release zip folder so continue from Step 1 of the release method.

### Usage

To display all the cached Kerberos tickets issue the command:
{% highlight powershell %}
klist
{% endhighlight %}
To purge all the cached Kerberos tickets issue the command:
{% highlight powershell %}
klist purge
{% endhighlight %}
### Screenshots

<img src="https://user-images.githubusercontent.com/33097451/274965338-4ac8bf58-9134-4c1d-9d00-efe0bee11b75.png"/>

<img src="https://user-images.githubusercontent.com/33097451/274966113-146cafe6-f3c8-43c6-ad8c-2ad417bfd129.png"/>

After speaking to the Sliver devs over at the BloodHoundGang Slack channel, they have said they would like to include it in the Armory. They advised me to open an issue on their Github so it can be verified for inclusion. This is now located here and has gone live on the official repo:

[https://github.com/BishopFox&#8203;/sliver/issues/1433](https://github.com/BishopFox/sliver/issues/1433)

This process has been lots of fun and a great learning experience. It gave me an opportunity to work with such an interesting C2 framework as Sliver and to improve my knowledge on how it's BOF framework operates. Hope it proved to be informative and happy hacking!





