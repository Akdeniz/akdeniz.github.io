---
layout: post
title: "Herhangi bir android uygulamasının data trafiğini dinlemek"
date: 2012-01-05 00:36
author: akdeniz
comments: true
categories: [Configuration]
tags: [android, emulator, fiddler]
---
{% include JB/setup %}

Elinizde bulunan bir android uygulamasının internete ne gönderip, internetten ne çektiğini merak ediyorsunuz.

Basit bir şekilde aşağıdaki komutu kullanarak emulator üzerinde oluşacak bütün trafiği dosyaya kaydedip, sonrada bunu Wireshark benzeri bir tool ile inceleyebilirsiniz.

{% highlight bash %}
cd "C:\Program Files (x86)\Android\android-sdk\tools\"
emulator.exe -avd Android30 -cpu-delay 0 -no-boot-anim -scale 0.8 -tcpdump data.pcap
{% endhighlight %}

Yalnız "ben oluşan trafiği runtime da görmek/incelemek/değiştirmek/filtrelemek/... istiyorum" diyorsanız, okumaya devam edin. Zira Fiddler gibi başarılı bir proxy ile bunları yapmanız oldukça kolay.

Sistemizde Android SDK nin kurulu olması gerektiğini sanırım belirtmeme gerek yok :) Bunun yanında <a href="http://www.fiddler2.com/fiddler2/" title="Fiddler HTTP Proxy" target="_blank">Fiddler HTTP Proxy</a> yi de indirip kurmanız gerekiyor.

**Fiddler ın kurulumunun ardından aşağıdaki gibi ayarlarının yapılması gerekiyor:**

<a href="/assets/media/posts/herhangi-bir-android-uygulamasinin-data-trafigini-dinlemek/1.png">![](/assets/media/posts/herhangi-bir-android-uygulamasinin-data-trafigini-dinlemek/1-300x200.png)</a>

Ayarların etkin olabilmesi için Fiddler i kapatıp, tekrar açmanız gerekiyor.

**Sonrasında Emulator u ayağa kaldırıp uygulamamızı kurmalıyız**

Emulatörümüzü çalıştıralım:
{% highlight bash %}
cd "C:\Program Files (x86)\Android\android-sdk\tools\"
emulator.exe -avd Android30 -cpu-delay 0 -no-boot-anim -scale 0.8
{% endhighlight %}

Uygulamamızı kuralım:(mackolik in android uygulamasını kobay olarak kullandık :))
{% highlight bash %}
cd "C:\Program Files (x86)\Android\android-sdk\platform-tools"
adb -s emulator-5554 install com.kokteyl.mackolik-1.apk
{% endhighlight %}

Buradaki “emulator-5554” emülatörümüzün ismi. Hali hazırda bağlı olan cihazların/emülatörlerin listesini “adb devices” ile gorebilirsiniz.

**Son olarak emülatörü, internete proxy üzerinden çıkacak şekilde ayarlamalıyız.**

*Settings &gt;&gt; Wireless &amp; Networks &gt;&gt; Mobile networks* yolunu izleyerek resimdeki gibi "Data enabled" in secili oldugundan emin olun.

<a href="/assets/media/posts/herhangi-bir-android-uygulamasinin-data-trafigini-dinlemek/emu_settings_2.png">![Internet erişimi](/assets/media/posts/herhangi-bir-android-uygulamasinin-data-trafigini-dinlemek/emu_settings_2-300x263.png)</a>

Yine aynı sayfadan "Access Point Names" e tıklayarak gelen ekrandan APN e tıklayın.

<a href="/assets/media/posts/herhangi-bir-android-uygulamasinin-data-trafigini-dinlemek/emu_settings_1.png">![APN Ayarları](/assets/media/posts/herhangi-bir-android-uygulamasinin-data-trafigini-dinlemek/emu_settings_1-300x200.png)</a>

Buradaki ayarların şu şekilde olması gerekiyor:
{% highlight bash %}
# Name : <herangi birşey girilebilir. ör: internet>
# APN : Internet
# Proxy : Fiddler ın IP adresi (bizim için bu deger 192.168.1.101)
# Port : Fiddler ın port numarası (eğer değiştirmediyseniz varsayılan 8888)
# Username : <boş bırakılacak>
# Password : <boş bırakılacak>
# MCC : 310
# MNC : 260
{% endhighlight %}

*Diğer ayarlar olduğu gibi bırakılmalı.*

**Bütün ayarları tamamladığımıza göre artık uygulamamızı çalıştırarak, Fiddler üzerinde veri alış-verişini görebiliriz.**

<a href="/assets/media/posts/herhangi-bir-android-uygulamasinin-data-trafigini-dinlemek/mackolik.png">![Mackolik Ekranı](/assets/media/posts/herhangi-bir-android-uygulamasinin-data-trafigini-dinlemek/mackolik-300x200.png)</a>

<a href="/assets/media/posts/herhangi-bir-android-uygulamasinin-data-trafigini-dinlemek/fiddler.png">![Fiddler Ekranı](/assets/media/posts/herhangi-bir-android-uygulamasinin-data-trafigini-dinlemek/fiddler-300x163.png)</a>

Başka bir yazıda buluşmak dileğiyle...
