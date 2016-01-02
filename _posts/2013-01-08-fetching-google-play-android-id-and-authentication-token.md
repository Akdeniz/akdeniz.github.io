---
layout: post
title: "Fetching Google Play Android ID and Authentication Token"
date: 2013-01-08 22:35
author: akdeniz
comments: true
categories: [Android, how-to, Programming]
tags: [android, market, phonesky]
---
{% include JB/setup %}

In order to use my <a title="google-play-crawler" href="https://github.com/Akdeniz/google-play-crawler" target="_blank">google-play-crawler</a> project in an android application, you have two option to get it work :

- requesting *username, password and android-id from user*, which is kind of lame! or 
- fetching *android-id and authentication token*, that google play application(*aka Phonesky*) uses, programmatically.

You can accomplish second option with these functions.

{% highlight java %}
private String getAuthToken() throws OperationCanceledException, AuthenticatorException, IOException {
  Account account = AccountManager.get(this).getAccountsByType("com.google")[0];
  AccountManagerFuture<Bundle> accFut = AccountManager.get(this).getAuthToken(account, "androidmarket", null, null, null, null);
  Bundle authTokenBundle = accFut.getResult();
  return authTokenBundle.get(AccountManager.KEY_AUTHTOKEN).toString();
}
{% endhighlight %}

{% highlight java %}
private String getAndroidID() {
  String[] query = new String[] { "android_id" };
  Cursor cursor = getApplicationContext().getContentResolver().query( Uri.parse("content://com.google.android.gsf.gservices"), null, null, query, null);
  if ((cursor.moveToFirst()) && (cursor.getColumnCount() >= 2)) {
    return Long.toHexString(Long.parseLong(cursor.getString(1))).toUpperCase();
  }
  return null;
}
{% endhighlight %}

To call these two functions, you need to grant these permissions in your manifest file.
{% highlight xml %}
  <uses-permission android:name="android.permission.GET_ACCOUNTS" />
  <uses-permission android:name= "android.permission.MANAGE_ACCOUNTS" />
  <uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />
{% endhighlight %}
