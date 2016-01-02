---
layout: post
title: "I've just met Mallory!"
date: 2013-04-28 04:17
author: akdeniz
comments: true
categories: [Configuration, how-to]
tags: [mallory, mitm, proxy, ssl, stripping, transparent]
---
{% include JB/setup %}

***Mallory!?***

Ok, I have to admit that I recently realized, such a tool named Mallory exists. So much so that I even started to develop <a href="https://github.com/Akdeniz/mitmsocks4j" target="_blank">mitmproxy4j </a>to intercept SSL traffic.

It was obvious that every tester needs such a tool to intercept applications' traffic to see what is going on over network. You may think that you can accomplish this by using a few iptables command and writing a tool to intercept and redirect traffic, but doing so for every application you want to examine, can be tedious and frustrating.

So [Intrepidus Group](http://intrepidusgroup.com/) did a great job to develop Mallory. Formally, Mallory is a transparent TCP&UDP proxy that is able to intercept any traffic over TCP and UDP. HTTP, HTTPS, DNS and SSH are some of them. Also its transparency allows tester to use it without any special configuration on client side. That means you can use it on your mobile phone by using built-in configuration options.

***Enough said, lets setup our Mallory!***

Mallory needs to live on a gateway, so first of all, you need a machine to operate as a gateway for clients those we want to intercept their traffic. So I installed <a href="http://www.ubuntu.com/download/desktop" target="_blank">Ubuntu 12.04 LTS</a> on a virtual machine with a bridge network.(Also there is a vm torrent link on their <a href="https://bitbucket.org/IntrepidusGroup/mallory/downloads" target="_blank">bitbucket </a>address but I didn't manage to download it because lack of seeders nowadays.)


I have Python 2.7.3 installed on Ubuntu. Also there is a bunch of packets to be installed in order to use Mallory. Run these commands:

{% highlight bash %}
sudo apt-get -y install build-essential mercurial libnetfilter-conntrack-dev libnetfilter-conntrack3 python-pip python-m2crypto python-qt4 pyro-gui python-netfilter python-pyasn1 python-paramiko python-twisted-web python-qt4-sql libqt4-sql-sqlite sqlite3 

sudo easy_install pynetfilter_conntrack

sudo ln -s /usr/lib/libnetfilter_conntrack.so /usr/lib/libnetfilter_conntrack.so.1
{% endhighlight %}

Now it is time for downloading Mallory source:

{% highlight bash %}
hg clone http://bitbucket.org/IntrepidusGroup/mallory
{% endhighlight %}
	
You have installed Mallory and it is ready to launch. But first we need an another interface for incoming connections.(Remember Mallory lives on gateway and gateways have at least two interfaces.(LAN/WAN)).

***PPTP Interface***

As a second interface, we will use Point-to-Point Tunnelling Protocol(PPTP). So we need to install a "pptpd" server.

{% highlight bash %}
sudo apt-get install pptpd
{% endhighlight %}
	
Now some configuration is needed to get PPTPD up and running.

{% highlight bash %}
sudo gedit /etc/ppp/chap-secrets
{% endhighlight %}

Edit this file like this: here "vpnuser" and "123456" are the username and password respectively that clients will use to connect PPTP server. 

{% highlight bash %}
# Secrets for authentication using CHAP
# client	server	secret			IP addresses
vpnuser pptpd 123456 *
{% endhighlight %}

Now run below command and edit PPTP server for local and remote ip addresses

{% highlight bash %}
sudo gedit /etc/pptpd.conf
{% endhighlight %}

You only need to uncomment and edit "localip" and "remoteip" lines.

{% highlight bash %}
# (Recommended)
localip 10.0.0.1
remoteip 10.0.0.100-200
{% endhighlight %}

You also may want to define DNS servers for windows pptp clients: Run

{% highlight bash %}
sudo gedit /etc/ppp/pptpd-options
{% endhighlight %}

and edit "ms-dns" lines like this:

{% highlight bash %}
ms-dns 8.8.8.8
ms-dns 8.8.4.4
{% endhighlight %}

Now we can restart the pptpd server and test it.

{% highlight bash %}
sudo service pptpd restart
{% endhighlight %}

To test it, we need to enable ipv4 forwarding and redirect incomming connections to network. Run:

{% highlight bash %}
sudo gedit /etc/sysctl.conf
{% endhighlight %}
	
and uncomment "net.ipv4.ip_forward=1" line. Run this command to get configuration changes.

{% highlight bash %}
sudo sysctl -p
{% endhighlight %}

Redirect traffic with this command and connect from client.

{% highlight bash %}
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
{% endhighlight %}

***Aha! Client it is!***

I used my android phone as a PPTP client. Configuration is pretty straightforward.

<a href="/assets/media/posts/ive-just-met-mallory/pptp.png">![pptp](/assets/media/posts/ive-just-met-mallory/pptp-300x248.png)</a>

If all goes well, you should connect to internet over configured gateway.

***Ok, I connect to PPTP server, so what?***

Now it is time to start mallory and its gui!

{% highlight bash %}
cd <install-dir>/mallory/src/
sudo python mallory.py
sudo python launchgui.py
{% endhighlight %}
	
In interfaces tab, you should see something like this. Here eth0 and ppp0 will be outgoing interface and inteface to be mitm-ed, respectively.

<a href="/assets/media/posts/ive-just-met-mallory/mallory_interfaces.png">![mallory_interfaces](/assets/media/posts/ive-just-met-mallory/mallory_interfaces-300x156.png)</a>

If you click "Apply Configuration" at this point, you can see traffic flowing at streams tab.(Assuming no protocol is selected and only "Debug All" rule exists). But it is raw data that is no stripping done on it.

So, if you want to strip SSL on, let's say, 5228 port, you need to define a protocol at Protocols tab like this:

{% highlight bash %}
ssl_1: sslproto.SSLProtocol:5228
{% endhighlight %}

*Be warned! I think there is a misconception about debuggability of defined protocols that, I couldn't see protocol-enabled traffic at streams tab. But "Db View" section under Advanced tab can always be used for listing streams.
*

***Do you have any last words?***

As I said, Mallory is a great tool. But it needs some contribution about feature-adding and bug-solving. I think it's authors don't have time nowadays. I'll see what I can do ;)  

Cheers! 

