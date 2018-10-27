---
layout: post
title: The Internet of Traffic Lights
description: experiment integrating arduino and raspberry pi via i2c to create an Internet of Things Traffic Light
actions:
  - label: "Learn More"
    icon: github  # references name of svg icon, see full list below
    url: "https://www.github.com/simonarnell/trafficLight"
---

This was a fun project I worked on back in 2016, it started with me learning to use the [Arduino](https://www.arduino.cc) micro-controller and quickly developed to an internet-connected [Raspberry Pi](https://www.raspberrypi.org) interfacing to the Arduino over [I2C](https://en.wikipedia.org/wiki/I²C) to control a 230V traffic light via a set of [opto-isolated](https://en.m.wikipedia.org/wiki/Opto-isolator) relays. 

I have broken this post into each of the exploratory steps I took to learn how to use the Arduino and its interfacing with the Pi.

# Arduino

The Arduino is a very popular micro-controller that through its integrated development environment provides a very accessible means to get into embedded systems programming. The Arduino Uno board that I have used in this experiment uses 5V TTL signals - for a technical description of what TTL (Transistor-Transistor Logic) means, National Instruments maintains an excellant knowledge base [article](https://knowledge.ni.com/KnowledgeArticleDetails?id=kA00Z000000P9yaSAC).

I started with learning how to control LEDs using the Arduino, then relays, LEDs controlled via relays and finally the 230V lamps of the traffic light. The Arduino sketch for this is pretty simple as it just steps through the four phases of a UK traffic light, consisting of:

- Red
- Red, Amber
- Green
- Amber

The sketch is as follows:

{% gist 6c9b61030d5537556d13c4769073c787 %}

## LEDs

The following circuit diagram illustrates how to wire up the Arduino to control LEDs. If you're an electronics beginner you may be wondering why there are resistors between the Arduino and LEDs, this is because the Arduino outputs a 5V signal and the LED which only drops 1.8V can become overloaded by the current that passes from the 3.2V residual voltage - therefore a small (470Ω) resistor is necessary. For a more in depth explanation, I suggest consulting the Wikipedia [page](https://en.wikipedia.org/wiki/LED_circuit) on LED driver circuits.

![leds](/assets/images/3AE7E898-AF6E-4E14-A198-0306841750C0.svg)

Below is a video of the LED traffic light circuit working:

<iframe src="https://www.facebook.com/plugins/video.php?href=https%3A%2F%2Fwww.facebook.com%2Fsimon.arnell%2Fvideos%2F10100452373378930%2F&show_text=0&width=560" width="560" height="311" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowTransparency="true" allowFullScreen="true"></iframe>

## Relays

The following circuit diagrams replaces the LED and resistor output of the previous with relays. 

![relays](/assets/images/9020114E-B996-4489-9D74-F98206CBF729.svg)

This circuit renders a pleasing audible click-clacking of relays firing.

<iframe src="https://www.facebook.com/plugins/video.php?href=https%3A%2F%2Fwww.facebook.com%2Fsimon.arnell%2Fvideos%2F10100452373074540%2F&show_text=0&width=560" width="560" height="311" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowTransparency="true" allowFullScreen="true"></iframe>

## Relays and LEDs

The next circuit reintroduces the LEDs and resistors of the first circuit, but they are instead controlled via the opto-isolated relays, these relays will be useful for ensuring safety later when dealing with mains voltage.

![relaysLeds](/assets/images/615D8A77-89A6-4A85-8697-9603389A05D5.svg)

<iframe src="https://www.facebook.com/plugins/video.php?href=https%3A%2F%2Fwww.facebook.com%2Fsimon.arnell%2Fvideos%2F10100452372645400%2F&show_text=0&width=560" width="560" height="311" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowTransparency="true" allowFullScreen="true"></iframe>

## Relays and Mains Lamps

The next experiment was to replace the LEDs and their _safe_ 5V DC supply with the traffic light and its 230V AC supply. N.B. Within the traffic light I discovered three 230V to 12V step-down transformers, these allow the light to operate using 12V halogen bulbs. 

![relaysMains](/assets/images/2DE920C7-F7B5-4625-9707-4B516B0F36C9.svg)

** DISCLAIMER Experiment with mains voltage at your own risk.** 

As you will notice, with an abundance of caution, I retired to the safety of my garden when I attempted this experiment, with the setup connected to a residual current device, just in case I or someone else acted as a return path for the current.

<iframe src="https://www.facebook.com/plugins/video.php?href=https%3A%2F%2Fwww.facebook.com%2Fsimon.arnell%2Fvideos%2F10100452372156380%2F&show_text=0&width=560" width="560" height="311" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowTransparency="true" allowFullScreen="true"></iframe>

# Raspberry Pi

The Raspberry Pi is great example of a single board computer, it has enough resources to even run modern operating systems such as Linux or Microsoft's Windows 10, whilst providing General Purpose Input and Output (GPIO) pins for interfacing with hardware. Unlike the Arduino Uno, the Raspberry Pi uses 3.3V CMOS logic.

For this part of the project, I wanted to expose a web service running on the Raspberry Pi to the internet. This required writing a web service that provides an interface to remote users to control the traffic light. The received instructions would then be translated into  the intended actions upon on the light by setting outputs to high and low signal levels accordingly.

I opted to create a user interface consisting of the below Scalable Vector Graphics-based traffic light, that when clicked, sent control messages back to a (nodejs)[https://nodejs.org] javascript web service via [socket.io](https://socket.io) web sockets

![ui](/assets/images/4FBA1824-D89C-7BBA-9912-DDA831B91DD4.svg)

## Circuit

Whilst the Raspberry Pi could quite easily control the traffic light relays with its GPIO, I was interested to experiment with the interfacing of the Raspberry Pi with the Arduino via the I2C bus. Due to the differing signal levels (5V and 3.3V) of the two devices, an additional level shifter component was needed to interface the two.

![final circuit](/assets/images/EBD3344C-6F21-43FF-94DF-F8C51BEF3993.svg)

## Demo

I registered the domain putonmyredlight.com, setup an A record to point to my public IP and punched a hole through my firewall for the web traffic to access the service hosted on the Raspberry Pi. I then streamed video of the experiment and announced the url on [Facebook Live](https://live.fb.com) from a mobile device and the below video is the unedited video from the live session. It was great to see people connecting from around the world within seconds of going live to experiment with it.

<iframe src="https://www.facebook.com/plugins/video.php?href=https%3A%2F%2Fwww.facebook.com%2Fsimon.arnell%2Fvideos%2F10100517090155990%2F&show_text=0&width=267" width="267" height="476" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowTransparency="true" allowFullScreen="true"></iframe>
