---
layout: post
title: "Google account creation : mobile vs web!"
date: 2013-08-19 20:07
author: akdeniz
comments: true
categories: [Android, Google]
tags: [account, android, automated, creation, google, mobile]
---
{% include JB/setup %}

I always wonder the API differences between web and mobile version of a service. Somehow some developers may think that is OK to throw away few security precautions on mobile versions. Not sure but maybe they think their application is more secure on mobile phones. But on the contrary, mobile platforms are susceptible to misusing as much as any platform.

**Such as?**

One of these services is account creation of Google. As you know, Google does not provide an API for this operation but you should go [here](https://accounts.google.com/SignUp) and enter *two-word captcha* with other required personal information. Next you have to verify your account with your *phone number via SMS or Voice call options*.

**Fair enough, what about mobile side?**

In case you are not familiar; android allows you to create an account to use with your android phone. While creating account on your phone, your phone is performing two JSON request:*(well, more than that, but others are irrelevant)*

- In first request, your *android id* is sent as well as with other informations like username, first name, last name, password, secondary name..etc, to fetch captcha.
- In second one, same informations in first request in addition to captcha token and captcha answer are sent.

**So?**

There are two unknowns down here : *android id* and *captcha answer*.

- If you are familiar with [google-play-crawler](https://github.com/Akdeniz/google-play-crawler), it is easy to generate android id.
- For captcha, there are services like [Antigate](http://antigate.com), [DeathByCaptcha](http://www.deathbycaptcha.com)...etc

**Antigate, what..!?**

Captcha, retrieved from Google, is a *one word* and simple enough: In proof of concept that I have written, I observed that takes less than *10sec* to fetch each captcha answer successfully.

I have to admit that I didn't know crowd-sourcing is that responsive, high success rate and cheap. One dollar for thousand captcha? Gotta be kidding!

**Anyway here are some obvious questions?**

Two word captcha for web and one word captcha for mobile? For usability? Ok, I can understand that but how many users use this service to create account for their phone? Does it really need better usability?

Removing phone verification? Isn't that more proper to use this verification on phone?

Thanks.
