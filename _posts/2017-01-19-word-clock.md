---
layout: single
title: "Building a Word Clock"
date: 2017-01-19 21:59:00
tags: [projects, word clock, embedded]
imgDir: /assets/img/wordclock
---

A **word clock** communicates the time with letters rather than numeral digits or hands. They're unique and make for great gifts, especially when personalized.

![A custom word clock.][img-assembled-1]

I made this word clock as a Christmas gift for my dad in 2016. This post is a high-level retrospect of its development; detailed documents, including the schematic, parts list and firmware, are linked toward the end.

I enjoyed this project as a balance to the scientific mindset that we bring ourselves to when solving technical software problems. It was nice to work on something creative with tangible, human-facing aspects.

# Overview

- Tech: ATMega328P + DS3231 + 98 5mm LEDs
- Chassis: IKEA Ribba shadow box
- Build date: November - December 2016

# Design

The word clock is a popular project among electronics hobbyists. It's taken [many different forms][link-wordclocks].

To me, the appeal of the word clock is its simple elegance. It communicates the time in a manner that relieves you of the oft-gratuitous precision of traditional digital clocks. The word clock is colloquial and casually imprecise, but accurate.

[scottbez1's instructable][link-scottbez1-instructable] captures that philosophy well, so I based my design on his, making changes along the way.

# Letter Mask

![Letter mask][img-letter-mask]

The letter mask is the clock's face, sitting in front of an array of LEDs and transforming each one's light into an individual letter.

I made a letter mask in GIMP sized around the chassis' dimensions. It uses AppleGothic for a sleek look, and has empty space around the edges for aesthetics and to leave space for non-LED components. I placed each letter of the mask in its own cell of a grid; the font's effectively monospaced.

I printed several copies out on transparencies at Kinko's, cut three to fit, and carefully aligned and adhered them to each other using clamps and double-sided tape. Stacking them prevents light from bleeding through any black space.

I cut a hollow rectangle out of amber construction paper and adhered it to the front of the transparencies to provide some color and close any openings in the shadow box's viewable area. I attached a black garbage bag to the rear of the masks to diffuse the light coming from the LEDs, making the display viewable straight-on and consistently bright.


# LED Array

![LED array, front][img-led-array-front]

The LED array is an array of 98 LEDs, each of which pairs with an individual letter of the letter mask.

I made the LED array by punching a bunch of carefully aligned holes in a piece of cardboard. After soldering a resistor to the anode of each LED, I placed each LED in a hole and soldered each resistor to each other to provide a common power connection. I connected the cathodes of each word-forming group of letters to each other, and brought out a wire from each common cathode for connection to the driver board.

Later, I attached cardboard baffles to the front of the array to minimize inter-word light bleeding. I cut them from a cereal box and held them in place with hot glue.


# Driver Board

![Driver board][img-driver-board]

The driver board gates the ground connection of each word-forming LED group.

Each word sits in front of a Darlington transistor of a ULN2003A chip whose input is a bit of a logical 24-bit shift register formed by three 4094 chips. The 4094s get their register values from a separate control board. I borrowed Scott's circuit and soldered this board by hand.


# Control Board

![Control board][img-control-board]

The control board listens to the user interface, figures out the time of day, and controls which words the driver board lights up. I designed this circuit myself and soldered the board by hand.


# Microcontroller

I chose an ATMega328P for the clock's brain since I had a few lying around and AVRs are easy to interface with thanks to the Arduino environment. That said, any chip with basic peripherals and a modest amount of memory - I2C, PWM, analog in, 16K ROM - would have been fine.


# RTC

The MCU gets the time from a DS3231 RTC. I got one from Adafruit that's wrapped up in a convenient eight-pin breakout and sports a coin cell battery backup. This backup means the time isn't lost when the clock is unplugged, at least for 3 to 4 years - the RTC draws about 0.84uA in standby and the battery I picked supplies 38mAh.

The DS3231's accuracy depends on its environment's temperature. In the weather conditions of its home of paradisiacal southern California, it's accurate in a +-2 PPM window. In other words, for every million seconds of actual time that pass, the clock's time will stray by at most two seconds in either direction. More practically, this means the clock will stray by no more than about a minute per year.

I considered another design that opted for an external crystal rather than an RTC. This would save on cost, but require custom time-calculating firmware, be less accurate, and be a challenge to battery back.


# Power

A 7.5V, 1A wall adapter feeds the clock. This is fed raw to the LED array, and regulated down to 5V for the control and driver boards. I chose 7.5V instead of 5V because I didn't want too much variance in each LED's brightness when accounting for resistance and diode forward voltage drop tolerances.

I was careful to pull down the driver board's outputs so that while the MCU is booting up the driver board won't turn on all 98 LEDs simultaneously and overload the wall adapter. I picked out an adapter with a fuse just in case.


# User Interface

![User interface][img-user-interface]

The user interface is comprised of two buttons for time adjustment (hour advance and minute advance) and a potentiometer for brightness adjustment. I sized the buttons to accommodate my dad's large fingers (he's 6'5"). The potentiometer controls the output enable pins of the shift registers using PWM for a dimming effect.

Each interface component is panel-mounted to the rear of the shadow box. I hand drilled slots for each; a metric step bit was especially handy, since I didn't have any metric bits coming into this project.


# Firmware

After initializing each pin used, the firmware constantly polls the inputs and adjusts the RTC's time and output enable PWM pin width as needed. It periodically queries the RTC for the current time and updates the driver board's shift registers as needed, once every five minutes.

Firmware comes naturally to me now thanks to years of experience, so this project's was pretty straightforward. The most fun I had here was deciding how the clock should respond to the user pressing its buttons. Since the clock is only precise within five minutes, should the minute advance move the time forward by one minute or by five? How long should a user have to hold a button before that button's action is repeated, if ever, considering the target user? Should the clock reset its second counter when its time is advanced? I spent nearly as much time answering these questions as I did writing the rest of the code.


# Assembly

Those are all of the pieces! Here are some shots of the assembly process and completed project.

![LED array, back][img-led-array-back]

![Disassembled word clock][img-disassembled-1]

![Disassembled word clock][img-disassembled-2]

![Assembled word clock][img-assembled-2]

![Assembled word clock][img-assembled-3]

![Assembled word clock][img-assembled-4]

# Documents

Interested in creating your own word clock? Feel free to reach out to me.

- [Parts list][link-parts-list]
- [Schematic][link-schematic]
- [Firmware][link-firmware]
- [Letter mask][link-letter-mask]


# Lessons Learned

- Source your components from reliable sellers on an as-needed basis. I used LEDs purchased from a Chinese eBay seller several years ago, and several were nonoperational before use. Several more burned out within a few hours of use and needed to be replaced. Desoldering isn't as easy as soldering.
- Size your project components precisely given the chassis before designing anything. I sized the letter mask without properly accounting for the electronics boards that would sit behind it, and had to fit the boards in a very limiting empty space by hacking up the perfboards and making a bunch of serial rather than parallel electrical connections
- Review mechanical diagrams closely before purchasing components, and ensure that you have the right tools to mount things. I made incorrect assumptions about the panel-mounted interface components and had to use some hacky drilling techniques to work around missing bit sizes and non-circular hole shapes. A laser cutter would have been lovely here.
- If you have this many electrical connections, design a PCB and get it fabricated rather than soldering everything. I soldered everything by hand and all of it, especially the entire LED array, was tedious. Your time is valuable and your project could use the reliability.

# Where to Go Next

I'd like to try making this project again with a PCB rather than hand-soldered wiring. I'd also like to try powering the clock at 5V using a microUSB adapter so that it can be powered by phone chargers and potentially computers' USB ports.

Thank you for reading!



[link-wordclocks]: https://www.google.com/search?site=&tbm=isch&source=hp&biw=1216&bih=699&q=word+clock+diy
[link-scottbez1-instructable]: http://www.instructables.com/id/Sleek-word-clock/
[link-parts-list]: https://docs.google.com/spreadsheets/d/17tVfDLt-0LMc_5vMZl31iws5Pd2t-Ol0GwKcZCI1BNE/edit?usp=sharing
[link-schematic]: https://drive.google.com/file/d/0B25ZTvO5IWPTbkVDSGpxVWZGc0k/view?usp=sharing
[link-firmware]: https://github.com/PumpMagic/WordClock
[link-letter-mask]: https://drive.google.com/file/d/0B25ZTvO5IWPTaTBoUm1YUEozOXM/view?usp=sharing

[img-assembled-1]: {{ page.imgDir }}/IMG_0061.JPG
[img-letter-mask]: {{ page.imgDir }}/IMG_0088.JPG
[img-led-array-front]: {{ page.imgDir }}/2016-12-08 18.23.08.jpg
[img-driver-board]: {{ page.imgDir }}/2016-12-09 20.18.55.jpg
[img-control-board]: {{ page.imgDir }}/IMG_0083.JPG
[img-user-interface]: {{ page.imgDir }}/IMG_0103.JPG
[img-led-array-back]: {{ page.imgDir }}/2016-12-08 18.16.55.jpg
[img-disassembled-1]: {{ page.imgDir }}/IMG_0081.JPG
[img-disassembled-2]: {{ page.imgDir }}/IMG_0092.JPG
[img-assembled-2]: {{ page.imgDir }}/2016-12-14 05.23.54.jpg
[img-assembled-3]: {{ page.imgDir }}/IMG_0079.JPG
[img-assembled-4]: {{ page.imgDir }}/IMG_0055.JPG