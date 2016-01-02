---
layout: post
title: "UnPacking/Packing Android Application"
date: 2012-09-23 19:04
author: akdeniz
comments: true
categories: [how-to]
tags: []
---
{% include JB/setup %}

In this post, you can find my pack/unpack notes for Android APKs(of course for educational purposes only ;)).

To accomplish this, you basically need JDK, Android-SDK(no surprise!) and <a href="http://code.google.com/p/android-apktool/" target="_blank">apktool</a>. 
 
First we need to decode our application to “smali” code with apktool.
{% highlight bash %}
apktool.bat d  sample.apk .\sample\
{% endhighlight %}

I usually decode only code part of application with “-r” option if I don’t want to edit resources. (Resource decoding and building can sometimes be error-prone! )

Now we have the smali code under ".\sample\smali\" directory and can edit it easily. If you are familiar with java bytecode, it will be no hard to understand and edit this code. (much more reasonable it is!)

Here is a smali assembly of simple HelloWorld class.
{% highlight java %}
.class public LHelloWorld;
.super Ljava/lang/Object;
.method public static main([Ljava/lang/String;)V
    .registers 2
    sget-object v0, Ljava/lang/System;->out:Ljava/io/PrintStream;
    const-string	v1, "Hello World!"
    invoke-virtual {v0, v1}, Ljava/io/PrintStream;->println(Ljava/lang/String;)V
    return-void
.end method
{% endhighlight %}

You can always refer to Dalvik Opcodes table from <a href="http://pallergabor.uw.hu/androidblog/dalvik_opcodes.html" target="_blank">here</a>.

After we edited our code we can now reconstruct our application apk with apktool.
{% highlight bash %}
apktool.bat b .\sample\ .\sample_edited.apk
{% endhighlight %}

In order to install this apk to our phone or emulator, it needs to be signed properly. To do this we should generate a key for signing:
{% highlight bash %}
keytool -genkey -v -keystore my.keystore -alias myandroidalias -keyalg RSA -keysize 2048 -validity 20000
{% endhighlight %}

It will ask for some information that can be entered freely. 

Now we can sign our apk by using generated keystore.
{% highlight bash %}
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore my.keystore .\sample_edited.apk  -signedjar .\sample_signed.apk myandroidalias
{% endhighlight %}

Android needs apks to be aligned with 4-byte boundaries to be able to memory-map them.
{% highlight bash %}
zipalign.exe -v 4 .\sample_signed.apk .\sample_aligned.apk
{% endhighlight %}

Finally if all operations were executed successfully, we can install our edited application for testing. (in my case, to emulator!)
{% highlight bash %}
adb -e install .\sample_aligned.apk
{% endhighlight %}

That is it! You can enjoy your non-restricted, ad-free and maybe new-featured application ;)
