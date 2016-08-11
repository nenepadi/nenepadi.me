---
layout: post
title:  "My first hack with the board - Part 1"
date:   2014-06-13
description: "But what does internship really mean? Through my thoughts, I have come to realise that internship is work for those who want to prepare themselves for employment after school."
tag: [arduino, electricals, hacks, traffic-light]
categories: [arduino]
share: true
comments: true
project: false
---

I am not so intelligent when it comes to electronics or electricals or physics. But with the arduino board am ready to hack on to do awesome stuffs. I was introduced to arduino by [Elite education](http://www.elite-education.org). Thanks to **Chelsey** and his team. What makes all this interesting is the fact that I didn't know what to really do with it. With time though I read through some hacks and I was delighted. At that point I really wanted to do something with it. There was this assignment given us by a lecturer and I suggested we used the arduino board. With my suggestion agreed to i had to setup really quick.

<!-- more -->

Arduino boards are quick to set up. Basically I found all the information I needed [here](http://www.arduino.cc). After setting up and going through some few examples, I started to tackle the task given us by the lecturer, **a traffic light system**. First since the task was a group task I had to put everyone together and come out with ideas on how to do it. At first we thought of making it big, but without the resources we were forced to use the only thing I had, **the arduino board, leds, wires and the basic stuffs that come with the arduino**.

Building the hardware part of the whole system was a real tussle. I had to go back and read some more on electricals. At the end I did something really simple. This is what I used:

* 5 leds (2 reds, 2 greens and 1 amber)
* A breadboard
* The arduino board
* Wires
* Usb cable to connect the board to my PC

#### The following are the steps I took to setup the hardware:

* __Join the longer part of the leds together with a wire__. Basically a soldering iron will do the magic but I had none so I used my hand. I took a red, yellow and green led each and I joined the longer pins to one another and afterwards joined it to a wire. The longer part of the led is _negatively charged_. Joining them together made it easier for me to have one common ground or "earth" as my local folks call it.

* __Join a wire to each of the shorter parts__. The shorter pin of the led is _positively charged_ and as such that will do the real action. I joined a wire each to this part of the led and fixed the whole setup to the breadboard, thus I have the traffic light system without the pedestrain system setup.

* __Extend some wires from the breadboard to the arduino board__. Next of, I had to extend the breadboard setup to the microcontroller(_arduino board_) to give it full functionality. I fixed the grounded part of the leds to pin GND. The +pin of red, amber and green leds were fixed to pin 12, 10 and 8 respectively. All on the arduino board.

Time to go. I will continue with how I fixed the pedestrain lights and then writeup the code I used for the functionality. Meanwhile you can try my setup if you have the tools. You can also find some help [here](http://www.instructables.com).

Ardios