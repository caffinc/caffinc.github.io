---
title: "Calculating the Key Check Value of a key"
author: admin
layout: post
permalink: /2018/04/key-check-value/
categories:
  - Java
  - Bouncy Castle
  - Key Check Value
  - Hardware Security Modules
tags:
  - java
  - kcv
  - bc
  - hsm
---
# What are Key Check Values?
When a key is transmitted securely to a third party, there is no easy way to find if the recepient got the same key or not, because it isn't sensible to communicate an otherwise securely transmitted key over a phonecall to validate it.
Checking only a few digits of the key might mean that errors in one of the unchecked digits might go unnoticed until it's too late. 
In some cases the complete key isn't even calculated in the clear, but imported directly into a Hardware Security Module which puts together the parts of the key to get the complete key.
In all these cases, the easiest way to check if all parties have the valid key is by communicating a Key Check Value (`KCV`). KCVs do not expose secure information and they do not contain information that can be reverse engineered to get back the key that was used to calculate them. They are in essence a `fingerprint` of the key (They are nowhere near as unique as fingerprints, but serve as a simple identifier), and can be used to compare if two keys are the same.

# What does it look like
A key check value is computed by simply performing an encryption operation on a set of empty bytes. The result of that encryption operation is the KCV. The encrypted data is usually 8 or 16 bytes long, but only the first 6 hex digits are transmitted as the KCV.

# How do I compute the KCV of a key?
Since the most common keys I'm working with at the moment are double-length DES keys, which are used in 3DES operations, I'll show you how to calculate the KCV for them:

We'll be using Bouncy Castle as the security provider for the example below. This may or may not be needed, your milage may vary.

```
private static String kcv(byte[] key) throws GeneralSecurityException {
    // Add Bouncy Castle Security Provider
    Security.addProvider(new BouncyCastleProvider());
    // Construct a Secret Key from the given key
    SecretKey skey = new SecretKeySpec(key, "DESede");
    // Instantiate a DESede Cipher
    Cipher encrypter = Cipher.getInstance("DESede/ECB/NoPadding", "BC");
    // Initialize the cipher with the key in Encrypt mode
    encrypter.init(Cipher.ENCRYPT_MODE, skey);
    // Encrypt an 8-byte null array with the cipher and return the first 6 Hex digits of the result
    return Hex.encodeHexString(encrypter.doFinal(new byte[8])).substring(0, 6).toUpperCase();
}
```

That is it! Let's look at an example - suppose I have a key `0123456789ABCDEFFEDCBA9876543210`. The key check value of this key generated by the algorithm above is `08D7B4`.

# What do I do if the KCV doesn't match?
Few things to consider if the KCV doesn't match:
1. Is the key really a DES key?
2. Are you sure you've entered all the values correctly?
3. Have you entered the key in as characters but you actually meant to input it as bytes? i.e. `Hex.decodeHex` vs `getBytes()`
4. Are you sure you're using the correct Key Variant for transmission of the key? Look at my previous post about key variants to find out more.

I hope this helps someone out! Happy coding!
