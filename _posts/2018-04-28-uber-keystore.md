---
title: "Creating a new UBER keystore in Java"
author: admin
layout: post
permalink: /2018/04/uber-keystore/
categories:
  - Java
  - Bouncy Castle
  - Uber Keystore
tags:
  - java
  - uber
  - bc
  - keystore
---
# My quest for creating a new keystore from scratch
I had to store a key in a keystore for a project I've been working on. Unfortunately all examples I could find online were how to create a keystore using some sort of keystore tool. I wanted to create a new keystore in Java, not in some third-party tool. I can't believe how annoyingly difficult it was to find it.

# What is a Keystore?
Chances are that if you're here, you already know what a Keystore is. A keystore is like a map, containing an alias->key mapping. The keys may or may not require a password, and the whole keystore itself may or may not require a password.

# What is an UBER keystore?
I use BouncyCastle for the crypto work in my project, so I use BouncyCastle's keystore format, called UBER. You can also use this coded (with minor modification) to create a JKS, or Java Keystore.

# How do I create one?
Like I mentioned - you need a few things to make a keystore:
1. A password for your keystore
2. A location for the keystore file
3. A key you want to store into your keystore (Why do you need a keystore if you don't have a key?)
4. A password for the key
5. Optionally BouncyCastle and Apache Commons libraries, though strictly speaking these are not necessary

Now that you have the basic ingredients to creating a keystore, let's look at the effort involved in creating a new keystore:

```
// Step 1: Create a new "Secret Key" to be stored in the keystore
SecretKey key = new SecretKeySpec(Hex.decodeHex("0123456789ABCDEF0123456789ABCDEF".toCharArray()), "DESede");
// Step 2: Create a new instance of an UBER keystore
KeyStore keystore = KeyStore.getInstance("UBER"); // Change this to JKS if you want a Java Keystore
// Step 3: Load empty data into the keystore
keystore.load(null, null); // This is used to load an existing keystore from a (file)stream
// Step 4: Store the key into the keystore
keystore.setEntry("keyAlias", new KeyStore.SecretKeyEntry(key), new KeyStore.PasswordProtection("YourChosenKeyPassword".toCharArray()));
// Step 5: Write the keystore to the filesystem
keystore.store(new FileOutputStream("/usr/local/keystores/myNewKeystore.ubr"), "YourChosenKeystorePassword".toCharArray());
```

That's about it. `<rant>`*For these 5 lines of code, I had to search the internet for several days and go through stupid blog posts talking about how to use a keystore tool to create a JAVA KEYSTORE. What is wrong with you people?*`</rant>`

I hope this helps someone out there! Happy coding!