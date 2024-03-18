# Goodgreat DS3

The Goodgreat DS3 is a clone of the 1980s sampling synthesizer card for the Apple II, the Greengate DS3. It's only somewhat obscure, having been used by several bands in the 1980s including Mainframe ([who were involved with the development of the card](http://mainframe-music.info/the_ds3_digital_sampler.html)), The KLF, Portion Control, and even a George Michael album (Faith).

![Photo of the Goodgreat DS3](https://github.com/schlae/goodgreat-ds3/blob/main/GoodgreatDS3.jpg)

The sampler card supports four simultaneous voices for 8-bit digital playback, each with an adjustable playback rate (pitch). It can also record. The sample rate can go up to 30KHz although this limits the sample lengths considerably--the card uses the Apple II host's main memory to store all the samples!

The card works in the Apple II, Apple II+, Apple IIe (including the Platinum), but not the IIgs or the IIc. The card will work with a joystick. The more RAM you have, the better.

Warning: Not all the components are readily available. In particular, the ZN428E DACs, ZN448BE ADC, AM9513 timer, and MC6844 DMA controller are no longer made and will need to be purchased from a surplus electronics store or online auction site. Note that parts brokers in China nearly always paint-and-remark their chips even if they are legit. It's a risk. One more note about parts: The 6522 I/O chip can be found for sale brand new as the WD65C22. *Do not use this device--it has several electrical differences that make it incompatible with the card*.

[Schematic](https://github.com/schlae/goodgreat-ds3/blob/main/GreengateDS3.pdf)

[Bill of Materials](https://github.com/schlae/goodgreat-ds3/blob/main/GreengateDS3.csv)

[Fab Files](https://github.com/schlae/goodgreat-ds3/blob/main/fab/GreengateDS3-Rev1.zip)

## Fabrication Notes
This is a 2-layer board. The board dimensions are 7.5 inches x 3.03 inches (191mm x 77mm). The thickness is the standard 0.062" (1.6mm). You may choose to add an edge bevel to make the card edge fingers easier to plug into your Apple II, but this is optional. You might also want to get the gold plating which will make the card more reliable, although it can be quite a bit more expensive to go this route.

Don't forget to pick a fun color for the soldermask.

## Assembly Notes
I recommend buying sockets for all the hard to find parts (IC3, IC9, IC14-IC18, U9).

The original card had a bundle of 5 audio cables, one per voice and one for the recording input, going from J4-J7 and J2 out to a DE-9 connector that plugged into a simple passive breakout box. The passive breakout box also had a switch wired up that could short all the voice outputs together to create a single, mono mix. (Electrically this is OK because the voices all have series resistors on the card).

![Circuit diagram of the wiring for a 3PDT switch to the audio outputs](https://github.com/schlae/goodgreat-ds3/blob/main/switch.png)

I don't have an original breakout box so I don't know how the DE-9 connector was wired up. Feel free to connect it however you see fit.

PL1 is funny because the footprint is meant for a 20-pin connector but the shipping card uses a 16-pin connector. It is up to you which to use. The extra pins are VCC and GND, neither of which are necessary for a simple diode-matrix keyboard.

### The Keyboard (Optional)
The original Greengate keyboard uses a simple keybed connected with series diodes in a scanning matrix. There are 8 groups of typically 8 keys each. The groups are driven by pins 1-8 of the PL1 (16-pin) connector. Diodes are wired in series with each key so that when the key is pressed, the cathode of the diode gets connected to the group. The anodes of the diodes are ganged together: every 8th note shares the same anode line. See the following table for the wiring.

| Pin  | Function |
|------|----------|
| 1 | Group 1 (lowest) |
| 2 | Group 2 |
| 3 | Group 3 |
| 4 | Group 4 |
| 5 | Group 5 |
| 6 | Group 6 |
| 7 | Group 7 |
| 8 | Group 8 |
| 9 | Note 4 |
| 10 | Note 1 |
| 11 | Note 3 |
| 12 | Note 0 (lowest) |
| 13 | Note 2 |
| 14 | Note 6 |
| 15 | Note 7 (highest) |
| 16 | Note 5 |

**Caution: Do not short any group line to any other group line. Do not short any note line to any other note line. This will damage your 6522.**

You might be able to take an existing keybed and modify it to work like this, but I haven't tried it out myself.

## Trying it Out
Install the card in slot 5. It won't work in any other slot.

The disk images for the original Greengate DS:3 can be found on archive.org. You'll want at least the Master disk and the Examples disk. This works better with a system with two drives: put the Master disk in the primary drive and the Examples disk in the secondary drive, then turn on your Apple II. When the menu appears, hit 3 (SOUND PLAY\/SEQUENCE). At the `OLD SETUP? (N):` prompt, enter `*`. Then enter N followed by Y, and when it asks for a filename, put in `DEMO1`. If all goes well, it will ask `OLD SETUP? (Y):` and you can just hit return. The program should then display all the voices, so hit return, and you should see a blinking cursor in the upper right hand of the screen. Then hit `!` to listen to the sound playback.

For audio recording, you'll need to adjust the gain trimpot RV1. Just use it like a volume control.

## Troubleshooting

Most problems I've seen with this card are caused by one of two issues
* Soldering problems, like cold solder joints. Check the board over very carefully!
* Counterfeit or dead chips. It helps to have spares on hand.

If you try to play the demo song and the metronome/timer isn't working (flashing block in the upper right corner of the screen) you may have a bad 6522. These can easily be damaged if you short the group or note lines of the keyboard connector, so be extra careful if you are making custom wiring for a keyboard. The AM9513 timer chip can also cause this issue.

For audio issues:
* Can you see an audio signal on pin 5 of each of the four DAC chips (IC15-18)?
* Do you measure 5V on the output of REG1?
* Does the VREF pin (pin 6) of each of the four DAC chips (IC15-18) measure at about 2.5V?
* For recording, have you checked RV1?
* For recording, is there 1.25V on pin 3 of U10?
* For recording, is there an audio signal on pin 6 of IC14? Check with an oscilloscope

For general digital troubleshooting, use an oscilloscope to check
* Does the address decoder work? Check outputs of U8 (pins 9-12)
* Check for read and write on the timer, pins 9 and 11 of U9
* Check for a 7MHz signal on pin 6 of the timer (U9)
* Do you see output pulses on pins 3, 2, 38, and 40 of U9 during sample playback?
* Do latched U6 and U7 get set and then get cleared correctly?
* Does the DMA controller attempt to access the Apple II memory? Check pin 34 of IC3

If the card can play back a sound but then stops after that sound, check pin 33 of IC3 to see if it is transmitting an interrupt. Check pin 21 of IC9 to see if it generates an interrupt after detecting the IC3 interrupt request.

If the Apple II crashes hard, check C2. I've found that this delay cap, which controls the DMA state machine, should not be larger than 680pF.

## Principles of Operation

The design is simple yet clever.

The timer IC U9 is configured to generate a periodic pulse for each sample to go out to each DAC. It contains five individual timers, one for each output DAC and one for the input ADC. The timer rate controls the sample rate and therefore the playback pitch. 
 Each timer pulse goes to a latch (U6, U7) that asserts the DMA request line for one of the four channels of the DMA controller IC3. This is the MC6844 DMA controller, an 8-bit DMA controller from the 6800 line which works fine in a 6502 system like the Apple II. A small state machine (U2, U3, U4) syncronizes it with the 6502 bus cycle on the Apple II, allowing it to take control of the bus from the 6502. The DMA controller then drives the bus with the address of the current sample data using IC1 and IC2, reading data from main system RAM and latching it in one of the four DACs, IC15, IC16, IC17, and IC18. Sounds from the DACs are available as four individual audio channels but can be mixed by shorting outputs together. Resistors R9, R10, R11, and R12 mix the audio together in the analog domain.

The DMA controller sets the length (in samples) of each sound, and when it runs out of data, it triggers IC3\_IRQ. That goes to the 6522 which can be configured to trigger the IRQ\# line going to the Apple II bus. The software can then reconfigure the DMA controller to play back the same sample again (looping) or to stop playing that sample. The 6522 is also used to read the musical keyboard.

Record mode works in a similar way but only for a single channel (the ADC).

## License
This work is licensed under a Creative Commons Attribution-ShareAlike 4.0
International License. See [https://creativecommons.org/licenses/by-sa/4.0/](https://creativecommons.org/licenses/by-sa/4.0/).

