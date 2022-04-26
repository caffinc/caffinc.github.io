---
title: "My Plant Pet"
author: admin
layout: post
image: assets/images/2021/09/myplantpet.webp
permalink: /2021/09/my-plant-pet/
featured: true
categories:
  - technology
  - IoT
tags:
---

## The motivation
Working from home has taken a toll on all of us. I say that despite the fact that nothing significant has changed for me personally - I still enjoy sitting on the couch and eating snacks and watching TV. I could do that professionally but sadly there are too many applicants and too few roles available for that job. Thankfully I'm also a developer and I can continue doing my work at home.

I've slowly added more greenery to my house and have also, undoubtedly, added more technology as well. While I would love to say that the plants have brought me as much joy as the tech, sadly that is not always true. They die. A lot.

![Dying Peace Lily](/assets/images/2021/09/dyinglily.jpg)

My quest to understand these plants better so they don't keep dying on me took a turn for the weird. I wondered if I could talk to these plants and I stumbled upon [Singing Plants](https://www.youtube.com/watch?v=3rlTIPiLYHo). Wellness, oneness, zen-ness and all that mumbo jumbo aside, I wanted to make one for myself because *it was **cool***.

I got into building IoT devices (which is turning out to be a career changing hobby), and as part of that decided to build a climate-controlled growth chamber for the plants. That's a long running project for another day. However, my encounter with the Singing Plants, and my journey to build something cool takes a small rest-stop here today.

Imagine you come home tired and weary from a long day. You sit on the couch and videos play in the background as you mindlessly scroll through memes on your phone. "Ho hum..." you say as you look around at the plants filling up your living room, wilting away. Prepare to say goodbye to all that! Gone are the days when your plants just sit there, doing nothing (One could argue they produce oxygen, but so do the trees outside that I don't water and that don't also die). I give you, **MyPlantPet**.

<iframe
    width="640"
    height="480"
    src="https://www.youtube.com/watch?v=TCHzfAoYBaU"
    frameborder="0"
    allow="autoplay; encrypted-media"
    allowfullscreen
>
</iframe>

(Background music by [Bensound](https://www.bensound.com/))

Bring this lovely succulent home, turn it on, pair with your phone, and you'll be greeted with a smiling plant that's happy to be around you! Walk away, and it goes to sleep *automagically*. Walk back in and it turns back on, ready to be played with - but only with you. That's right, you've paired with the plant, and it has eyes only for you. Touch the plant, and it will respond with a smile or a radical display of flashing lights! Stranger walking by and interacting with the plant? Nothing. You walk in the room and suddenly it gets just a little brighter for your little buddy!

![MyPlantPet at work](https://i.imgur.com/D53Mu33.jpg)

You can even put it on your desk at work to keep you company while you code away - perhaps helping you with some rubber ducking while making your workspace a bit more green!

## Sensing proximity with iBeacon
![iBeacon](/assets/images/2021/09/beacon.jpeg)

The proximity sensing is based on [iBeacon](https://en.wikipedia.org/wiki/IBeacon). Beacon devices broadcast a message to all devices nearby that might be listening. The iBeacon message consists of four parts:

1. Fixed iBeacon prefix
2. UUID
3. User data (4 bytes, Major and Minor)
4. Transmission Power

Using this information, a device listening to beacon messages can:

1. Identify that this is an iBeacon message, so might be interesting to it
2. Use the UUID to identify which device is advertising the beacon
3. Perform an action based on the user data
4. Calculate the transmitter's distance based on the transmission power and the receiving power

In our case, an Expo application is the Beacon transmitter, and the plant is the receiver. When the application says "I am transmitting you a message with `-56dB` power", then plant says "Well, I received it at `-65dB`, so you must be X meters away!". And if the transmitter is the owner of the plant based on the UUID, and the user provides a valid plant code, and the distance calculated is close enough, the plant turns on.

## Securing the interaction
This project is not the next secure vault, so please don't treat it as such. Bluetooth Beacons are not the most secure way of communicating. However, with Auth0, you can make at least part of the interaction very secure.

![Auth0](/assets/images/2021/09/auth0.png)

The application signs into Auth0 in order to obtain a unique ID which is converted to a UUID. When the plant turns on, it displays a 4-digit code that must be entered in the application to make sure that you broadcast an intentional message.

![Code on screen](https://i.imgur.com/I634ZhY.jpg)

The UUID and 4-digit code are transmitted as a beacon. When the plant receives this, it stores the UUID as a way of recognizing the owner of the plant in the future. Future messages from other UUIDs will be ignored. The Major field is used to transmit the 4-digit code, and the Minor field can be used to transmit commands.

## Making the plant interactive
The `ESP32` has [several touch sensitive pins](https://randomnerdtutorials.com/esp32-touch-pins-arduino-ide/). These are capacitive sensors that will sense change in currents when touched or interacted with. The idea then is very simple. Make sure that the plant has enough water to improve conductivity in the soil, and wire two pins from the `ESP32` through the soil, touching the plant. Now whenever you touch the plant, it will change the sensed current in the pins, which you can then translate to signals in your code.

## So how do you build one?
MyPlantPet uses IoT, Expo, Auth0, physics and some hand waving to bring your plants to the modern tech world. The experience consists of two parts:

1. An Expo mobile app that let's the plant know when you're near it
2. A smart plant you can touch and interact with

You can find the code for both parts [here](https://github.com/SriramKeerthi/PetPlant). To build it the way I have, you need the following software:

1. Expo (for the app)
2. Auth0 (for auth)
3. PlatformIO (for device software)

You will need the following hardware:

1. A TTGO T-Display ESP32 Microcontroller (About $12-15 on AliExpress)
2. A USB-C female plug (optional)
3. Some wires
4. Hot glue
5. A 3D printer to print a planter (or a planter you can use instead)
6. A soldering iron
7. Android device (for auth flow and beacons to work)
8. A willing plant

## Putting it together
### Device
Once the planter was printed, I made a couple of holes to allow for wires from the microcontroller to the be routed in. I built a USB-C power plug to power the device from the back (if you don't want to use the one built into it).

![USB-C plug](https://i.imgur.com/qONh0ra.png)

I hotglued the holes to make sure they're watertight. The USB-C is soldered to the 5V and GND pins, the touch sensor wires to pins 12 and 13. These wires can directly be plugged into the plant/soil later.

![Hot Glued Planter](https://i.imgur.com/8ltgFn2.jpg)

Now just place the plant in, put sufficient soil and water and you're good to go from a construction perspective.

### App
Expo development is pretty straight forward, but not when it comes to `Auth0` or `Bluetooth`. In order to get `Auth0` to work, I used `AuthSession` instead of the `React Native Auth0` client, and after [reading that the Redirect URL I use must be the one generated by `AuthSession`](https://github.com/GuyAvraham/expo-auth0-example-2020), everything worked great!

![App](https://i.imgur.com/umZGkc7.png)

Bluetooth Beacon generation however requires native code. Expo now allows running native code without ejecting first, and that is great. You can start a native application using:

```
expo run:android
```

I used the [React Native iBeacon Simulator](https://www.npmjs.com/package/@jaidis/react-native-ibeacon-simulator) library, but unfortunately there was a compilation error due to the library using `Nullable` which has been deprecated from Android. I manually modified the `BeaconBroadcast.java` file as follows:


```
import androidx.annotation.NonNull;
// import android.support.annotation.Nullable;
```

This fixes things temporarily, but adding any libraries to the project reverts the changes, and you have to make the changes again for your project to compile.

## Do you need more help?
This was a fun build where I got to learn a with a few different components on the app and device side that I got to learn, but I'm sure there's a lot of things that could have been done better or communicated better. You can reach out to me here or on LinkedIn to chat about this project, or about IoT, Green Tech, Security and Scaling in general!
