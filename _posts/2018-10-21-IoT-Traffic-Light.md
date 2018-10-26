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

I started with learning how to control LEDs using the Arduino, then relays, LEDs controlled via relays and finally the 230V lamps of the traffic light. The Arduino sketch for this is pretty simple as it just steps through the four phases of UK traffic light, consisting of:

- Red
- Red, Amber
- Green
- Amber

The Arduino is as follows:

```c
enum phases {
 red, redChanging, green, amber
};

phases phase, nextPhase;
unsigned long lightsTimer = 0;

void setup() {
  phase = red;
  for(int i=8;i<=10;i++) {
    pinMode(i, OUTPUT);
    digitalWrite(i,HIGH);
  }
}

void loop() {
  phaseChange();
}

void phaseChange() {
  if(millis() >= lightsTimer) {
    phase = nextPhase;
    switch(phase) {
      case red:
        digitalWrite(8, LOW);
        digitalWrite(9, HIGH);
        digitalWrite(10, HIGH);
        nextPhase = redChanging;
        lightsTimer = millis() + 10000UL;
        break;
      case redChanging:
        digitalWrite(8, LOW);
        digitalWrite(9, LOW);
        digitalWrite(10, HIGH);
        nextPhase = green;
        lightsTimer = millis() + 3000UL;
        break;
      case amber:
        digitalWrite(8, HIGH);
        digitalWrite(9, LOW);
        digitalWrite(10, HIGH);
        nextPhase = red;
        lightsTimer = millis() + 5000UL;
        break;
      case green:
        digitalWrite(8, HIGH);
        digitalWrite(9, HIGH);
        digitalWrite(10, LOW);
        nextPhase = amber;
        lightsTimer = millis() + 10000UL;
        break;
      default:
        digitalWrite(8, HIGH);
        digitalWrite(9, HIGH);
        digitalWrite(10, HIGH);
      break;
    }
  }
}
```

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

# Raspberry Pi and Arduino

## Logic levels

## Circuit

![final circuit](/assets/images/EBD3344C-6F21-43FF-94DF-F8C51BEF3993.svg)

## Demo

<iframe src="https://www.facebook.com/plugins/video.php?href=https%3A%2F%2Fwww.facebook.com%2Fsimon.arnell%2Fvideos%2F10100517090155990%2F&show_text=0&width=267" width="267" height="476" style="border:none;overflow:hidden" scrolling="no" frameborder="0" allowTransparency="true" allowFullScreen="true"></iframe>
