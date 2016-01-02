---
layout: post
title: "Listen any iOS Application Traffic over SOCKS Proxy"
date: 2013-02-23 03:58
author: akdeniz
comments: true
categories: [Configuration, how-to, iOS]
tags: [iOS, mitm, proxy, SOCKS, ssl]
---
{% include JB/setup %}

This post is about listening/intercepting data traffic of any application on your iDevice with the help of SOCKS proxifiying.

**You don't need to have your iDevice jailbroken to accomplish this operation.**

If you ever look at the wifi settings of iOS, you will see there is a section for *"Http Proxy"*. You may think it is only for HTTP proxies but by selecting *"Auto"* option, you can configure to use a SOCKS proxy. All you need to do is saving PAC(Proxy Auto Config) content below to a file with *.pac* extension and serving this file over a web server. I will use [script](/assets/media/posts/listen-any-ios-application-traffic-over-socks-proxy/nodejs.zip).

Here in this PAC file, we are defining host and port of our SOCKS proxy server. In your case, they may differ..

{% highlight js %}
function FindProxyForURL(url, host) {
	return "SOCKS 192.168.1.106:1080";
}
{% endhighlight %}

Now, it is time to configure our iDevice to reach and use this PAC file as seen at image below.

<a href="/assets/media/posts/listen-any-ios-application-traffic-over-socks-proxy/socks_pac.png">![socks_pac](/assets/media/posts/listen-any-ios-application-traffic-over-socks-proxy/socks_pac-300x225.png)</a>

For a SOCKS proxy, I'm gonna use my <a href="https://github.com/Akdeniz/mitmsocks4j" title="mitmsocks4j" target="_blank">mitmsocks4j</a> project which allows you to strip SSL from secure connections and dump data as a man in the middle.(It is currently at its early development phase and lacks many features, but does the job ;) ) 

You can start it like this to strip SSL from port *"443"*
{% highlight bash %}
java -jar mitmsocks4j.jar --securedports 443
{% endhighlight %}

SSL stripping operation needs you to add proxy certificate to truststore of your device. To do that; you will need <a href="http://www.apple.com/support/iphone/enterprise/" title="iPhone Configuration Utility" target="_blank">iPhone Configuration Utility</a>.

After installing this utility, create a configuration profile and add certificate for this configuration. Then install this configuration as shown in image below.

<a href="/assets/media/posts/listen-any-ios-application-traffic-over-socks-proxy/AddCertificate.png">![AddCertificate](/assets/media/posts/listen-any-ios-application-traffic-over-socks-proxy/AddCertificate-154x300.png)</a>

Last thing to be mentioned about this process is this utility only lists certificates those installed on your system. So you need to install proxy CA certificate to your system first, then you should be able to see it in certificate list.

If everything goes right, you should see files containing dumped traffic for each connection, on the same directory with *mitmsocks4j*.


