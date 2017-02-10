---
layout: single
title: "Building an Alien Animatronic"
date: 2017-01-18 21:05:00
tags: [projects, alien, animatronics, halloween, embedded]

#header:
#  image: /assets/img/alienanimatronic/IMG_0061_CROP.JPG

excerpt: "A fun little project to build a greeter for our Halloween party. \n\n![Skreee!](/assets/img/alienanimatronic/inaction.gif)"

imgDir: /assets/img/alienanimatronic
---

![Skreee!][img-action-gif]

If it's October and it's been a while since I've exercised my mechatronics skills... it's time to build an animatronic!

I helped host a Halloween party at a friend's place in the woods of Santa Cruz last year, and thought this guy might be able to greet our guests by peeking around the back of my car.

# Overview

- Tech: DC gearmotor + high power LED
- Mounting: Motor hub & mount + styrofoam ball + wooden dowels
- Build date: October 2016

# Design

My goal was to make a simple animatronic that could peek out the rear window of my car at our arriving visitors, as if to imply it was up to no good and wary of their presence. After falling in love with [this alien mask that kinda looks like a fish][link-mask-amazon] I knew I had to go with an alien theme. I had a futuristic looking cape from an old cosplay outfit that it could "wear", and figured I could pulse a green LED alongside the guy for some ambience.

# Animating the Mask

![Mask mounting assembly][img-mask-mount]

Getting radial motion from a shaft is easy enough: power a DC gearmotor through an H-bridge and control its speed with PWM. I did that with a Jameco motor, off-the-shelf H-bridge board and an Arduino. Here's the hard part, for someone coming from a technical rather than mechanical background: how do you leverage that motion to rotate a 23" diameter mask?

Here's my answer: a motor hub, long screws, wooden dowels, and lots of adhesive!

I took an off-the-shelf motor hub that fit my 12V gearmotor's shaft. The hub offers several threaded holes that one would normally use to attach a wheel. I couldn't just find 23" wheels - they'd weigh down the motor, even if I was willing to drop money on them. Instead, I took a few 2" screws, held them in the hub with nuts and thread locker (they free themselves easily under load), and crammed those screws into a styrofoam ball. I then drove a bunch of wooden dowels through that ball, in a way that approximated the shape of the mask, and held those in place using hot glue.

If there's anything I learned in mechatronics class, it's that something being unconventional doesn't preclude it being highly effective.

I programmed a basic state machine to make the mask rotate back and forth, occasionally doing a double-take and sometimes spinning in a complete circle, because why not?

# Pulsing the high-power LED

The cool thing about H-bridges is that it's while they're generally marketed for use with motors, they're really just power drivers that you can use to drive current through anything. The board I bought for driving the motor offered two H-bridges, so I used the one left over to drive the LED. I stuck the LED to a stock Intel CPU cooler I had lying around with some thermal paste and hot glue (I unfortunately couldn't get a hold of thermal adhesive tape in time). I didn't end up having to drive the cooler's fan, the passive cooling was plenty.

I programmed a basic state machine to pulse the LED on and off, usually slowly but sometimes quickly, and made it flicker sometimes as well.

# Mounting the Components

![Mask mounting assembly][img-components]

To mount the mask assembly vertically, I attached the motor to an off-the-shelf motor mount bracket, then attached that to a slab of wood that mounted perpendicular to another slab using a couple right angle brackets. The latter slab housed the dual H-bridge and Arduino.

I tucked the battery under the alien's cape and ran the LED behind the alien. I strapped the wooden platform to a box of dumbbells using a bungee cord. The little 12V motor generated a surprising amount of torque, and the platform would have found its way crashing down quickly were it not strapped down.

# Making the Alien Body

I tossed the electronic platform on a bunch of books that I stuck in place with wooden wedges. I then draped an old cosplay cape over the books and propped up one of its long sleeves on a high point of the car cabin. I got away with a simple approach because visibility of the alien was deliberately poor - details here didn't matter.

# Gallery

Here it is in action:

<iframe allowfullscreen="" frameborder="0" height="315" src="https://www.youtube.com/embed/xuWwpDGH8xo" width="560"></iframe><br />

# Documents

Interested in creating your own animatronic? Feel free to reach out to me.

- [Parts list][link-parts-list]
- [Firmware][link-firmware]

# Lessons Learned

- Don't be afraid to use adhesives like hot glue and thread locker, especially for one-off projects such as this. At first I tried making the mask mount using entirely removable fasteners like keps nuts and lock washers. Only after this failed several times did I resort to more permanent adhesives - and they worked first try.
- Don't take part mounting for granted! Mounting everything took me the most time out of anything in this project.

# Where to Go from Here

It was rewarding building something that felt alive and brought people enjoyment. Or gave them the heebie jeebies... I'll take that too. I'd like to play around with animatronics some more, and have already gotten some 16 gauge aluminum to do so.


**See you next Halloween!**



[link-mask-amazon]: https://www.amazon.com/gp/product/B0108FJW0A/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1
[link-parts-list]: https://docs.google.com/spreadsheets/d/1y8l7H8diMvLhjSow7c-xoUVFOXhhcHXPGHP0gJOvv8E/edit?usp=sharing
[link-firmware]: https://github.com/PumpMagic/OdysseyHalloween

[img-action-gif]: {{ page.imgDir }}/inaction.gif
[img-mask-mount]: {{ page.imgDir }}/2017-01-20 20.54.14.jpg
[img-components]: {{ page.imgDir }}/2016-10-29 00.22.08.jpg
