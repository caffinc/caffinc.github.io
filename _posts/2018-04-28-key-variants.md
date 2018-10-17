---
title: "Applying Key Variants manually"
author: admin
layout: post
permalink: /2018/04/key-variants/
categories:
  - Java
  - HSM
  - Key Variants
tags:
  - java
  - hsm
  - key variants
---
# What are Key Variants?
In systems involving two parties securely transmitting information to each other, there is always some sort of "key exchange" process. One of these processes is to first securely transmit a `ZMK` or `Zone Master Key`, also known as a `KEK` or `Key Exchange Key`, and then use that key to encrypt other `working keys`. The keys that might be transmitted using this `ZMK` are:

1. Pin Encryption Keys
2. Message Encryption Keys
3. MAC Keys

In order to increase the security of the systems a little more, generally a "variant" of the original `KEK` is used to encrypt and transmit the keys:

```
KEK         Variant 0
KPE         Variant 1
KME         Variant 2
KMAC        Variant 3
KPV         Variant 4
```

# What does it mean when an HSM transmits a key under a variant?
Simply put, a variant of the `KEK`, depending on the purpose, is chosen to encrypt the new key. That key is then sent across to the other party who takes the variant of the `KEK` on their end and decrypts it to get back the original key.

# What algorithms and keys are used for this?
One of the most commonly chosen algorithms for this is Triple DES/TDEA with ECB. The `KEK` is a 16-byte Double DES key, which is just expanded to 24-bytes during the Triple DES process.

DES keys are 56-bits long, and are accompanied by an 8-bit odd parity check value, bringing it up to 64-bits, or 8 bytes. Triple DES keys are made up of 3 DES keys, K1, K2 and K3. TripleDES is basically:

cipherText = enc<sub>K3</sub>(dec<sub>K2</sub>(enc<sub>K1</sub>(plainText)))

TripleDES keys are also known by their "length". If a key has a length 3, it means that K1, K2 and K3 are all unique. If the key has a length 2, it means that K1 and K3 are the same, but K2 is different. If the key has a length 1, it means that K1, K2 and K3 are all the same, which reduces the algorithm back to DES.

For the purposes of this example, the `KEK` and the key being transmitted will be 16-bytes, or double length keys.

# So how do I "apply" a variant? How do I get a variant of the `KEK`?
Let's say that you have a `KEK` that resembles the following *totally* randomly chosen 16-byte key (in Hex):

```
0123456789ABCDEF FEDCBA9876543210
^------K1------^ ^------K2------^
```

I want to transmit a MAC key. For this, I need to transmit the MAC protected under a variant 3 `KEK`, as mentioned in the table above - `KMAC`.

The process of applying a variant is to simply XOR the first byte of each key part with a hex value. This hex value is chosen from the table below:

```
Variant      Hex Value
0            0x00 (No Change, KEK stays the same)
1            0x08
2            0x10
3            0x18
4            0x20
5            0x28
```

I'm sure there's a great reason to pick these numbers in particular but I have no idea. To apply Variant 3 on the `KEK` above, we XOR as follows:

```
0123456789ABCDEF FEDCBA9876543210
^^               ^^
18               18
---------------------------------
1923456789ABCDEF E6DCBA9876543210
```

That's it. Use this new `KEK` Variant 3 for encrypting the MAC key to transmit to the other party, or use it to decrypt a MAC key sent to you under Variant 3.

# Example
Let's say I have the following *totally* random key that I want to transmit:

```
FEDCBA9876543210 0123456789ABCDEF
```

Under Variant 0, the `KEK` is `0123456789ABCDEF FEDCBA9876543210`, and the transmitted key would be:

```
1DF1B02B237AF9AE 1A4D672DCA6CB335
```

Under Variant 3, the `KEK` is `1923456789ABCDEF E6DCBA9876543210`, and the transmitted key would be:

```
6F5C6EBC91783189 FD88B1FEAF7AF890
```

You can obtain these by simply encrypting the data using TripleDES with ECB. To get back the original key, decrypt the data (duh).

# What if my KEK has several components?
If the KEK is sent across as different components to be imported directly to your HSM, it isn't possible to get the final KEK to apply a variant on it in order to do the necessary exports under a variant. In these cases, the way to import a key directly under a variant is to simply apply a variant on ONE of the components. This will ensure that when the components are XORed together to get the final key, it will be under the variant chosen. This is because of the nature of XOR.

Let's say the key KEK consists of 3 components, CP1, CP2 and CP3. To get the KEK, you simply do:

```
KEK = CP1 ^ CP2 ^ CP3
```

To get KEK3, or KEK variant 3, you would have to then do:

```
KEK3 = KEK ^ 0x18000000000000001800000000000000 (VAR3)
```

So to do this without exposing KEK, you simply apply VAR3 to one of CP1/CP2/CP3:

```
KEK3 = (CP1 ^ VAR3) ^ CP2 ^ CP3
```

This way the KEK is never compromised. You could also tell your HSM that you are importing a 4 component key, and import VAR3 as the last component.

# How do I decrypt a key encrypted by my HSM under a KEK/ZMK?
Sometimes you're given a key that's encrypted under a ZMK that you need to decrypt and get back the plain key. You don't want to import it into an HSM (which would be the *right* thing to do but you don't want to listen to good advice). Using Bouncy Castle as a Security Provider, the following code should do the trick:

```
Security.addProvider(new BouncyCastleProvider());
SecretKey zmk = new SecretKeySpec(Hex.decodeHex("0123456789ABCDEFFEDCBA9876543210".toCharArray()), "DESede");
Cipher cipher = Cipher.getInstance("DESede/ECB/NoPadding", "BC");
cipher.init(Cipher.DECRYPT_MODE, zmk);
byte[] key = cipher.doFinal(Hex.decodeHex("1DF1B02B237AF9AE1A4D672DCA6CB335".toCharArray()));
```

In a follow up post, I'll discuss about how to calculate Key Check Values, which are an essential part of transmitting keys securely to another party. I hope this helps you out!