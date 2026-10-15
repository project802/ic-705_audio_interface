# IC-705 Audio Interface

![Cover Photo](./images/ic-705_and_interface.JPG)

This project was done as a learning exercise for me, and a way to have some fun. I wanted to use my IC-705 with just an iPhone to drive digital mode communications, but limitations of both the radio and the phone meant things like straight USB or Bluetooth wouldn't work. There is a solution path with WiFi-based software or a Digirig, but I didn't want to be locked into a single app (even though I use it on my iPad and find it very good) or buy hardware when I thought I could make something myself and learn.

The result is a minimal solution that is ideal for remote or portable operation (such as POTA or SOTA) with iOS.

All said and done, I have got this working with iFTx to successfully complete QSOs!

In the end, this device is just an audio level shifter with isolation. It can be used with any device (PC, tablet, phone) that has an analog audio input (microphone or line in) and an analog audio output. Just tune the trim pots to hit the voltage levels you need.

Have a PC? Just use the built-in USB functionality or Wi-Fi interface. This adds no value, whatsoever, over those options. This is really for phones using inexpensive (or free) apps that require VOX activation.

## (Non-)Solution Problem Space

### USB Cable or Bluetooth

The radio presents a BT/USB interface to the iOS device, but there are two issues that prevent this from working:
- iOS does not allow for serial port interfaces over USB or BT so an app can't control PTT via the CAT interface. Even with serial port support, this may reduce the number of app choices to ones that specifically support CAT.
- IC-705 doesn't do VOX over USB or BT so we can't just send audio. Icom...come on! The firmware for this is *so tiny*!

There are also reports that the IC-705 has poor RFI rejection via USB so depending on what is plugged in, this could make digital mode operation difficult.

You can theoretically make these interfaces work if you manually activate PTT. That doesn't sound fun.

### Digirig

This is a valid solution. Cost is approximately $45 for a Digirig Lite and I'd estimate $20 (if you can buy individual parts) to make an adapter cable for the IC-705. I still can't confirm if the output levels to the radio are proper, but at least one person has said it functionally works.

https://forum.digirig.net/t/cable-for-icom-ic-705-and-mobile-ft8/10725

### Wi-Fi 

The app SDR-Control does work, but it is sold separately for iPhone and iPad. You would be nearly $100 into software to run on both devices. Its the best solution, objectively, but is costly and locks you into an app. If you change the radio, the app is useless. I happen to like how it works on my iPad, but I've seen some impressive work from other developers in the $2 to $4 range.


## Design Notes

The basis for the design is an [example circuit](images/Icom_example_interface.JPG) that Icom provides in their advanced manual. Addition of a capacitor on the MIC line is required to create a high resistance path to not activate PTT (the IC-705 uses the same pin for both MIC input and PTT).

Design files (PCB, schematic) can be found in the [KiCad directory](KiCad/).

A [screenshot of the schematic](images/ic-705_audio_interface_schematic.JPG) is also available for quick reference.

Icom doesn't seem to publish the MIC input electrical specifications. Through empirical testing, it seems the maximum IC-705 MIC input level before clipping is 50 mVpp.

The methodology was:
1. Set the iPhone volume to max
1. Set the FT8 software to call CQ at ~3000 Hz offset
1. Activate the voice recorder (VOICE TX -> REC/SET -> REC -> T1 -> record)
1. Set MIC gain to 50% (default level)
1. Adjust the trim pot until the meter hits 100%

I've observed about 0.7 dB of rolloff at 200 Hz vs 3000 Hz (could be the soundcard, could be the interface, I haven't looked harder), so that's why using the upper part of the sideband is good for setting max power level. The good news is that FT8 and FT4 use 500 Hz or higher offsets, so the rolloff shouldn't affect performance.

I landed on AF GAIN = 30 as that seems to be the highest value before output clipping occurs.

## Assembly Notes

This is a straight forward board to assemble. The IC-705 side uses a 3.5mm TRS for the speaker connector and a 2.5mm TRRS for the mic connector. I used a 3.5mm TRRS to connect to the phone side with the jumper soldered, but you can use two cables instead.

For PCB assembly, I recommend doing the SMT parts first, then the trim pots, then the cables.

The output capacitor that goes inline with the IC-705 mic input was left off the PCB to keep the footprint as small as possible. Put one leg into the PCB and the other towards the cable.

[DigiKey BOM](https://www.digikey.com/en/mylists/list/Q781V9DVDD) @ $7

[2.5mm TRRS cable](https://a.co/d/0cmaGAqM) @ $9 ($1.50/ea)

[3.5mm TRRS cable](https://a.co/d/0evUUoQW) @ $8 ($2/ea)

[3.5mm TRS cable](https://a.co/d/0eWTbZUC) @ $8 ($4/ea)

[USB audio interface](https://a.co/d/0cbzTN2W) (others will work, too)

[3D printable case files](https://www.printables.com/model/1877246-ic-705-audio-interface-box)

Below is an image of the cable routing and capacitor placement. It is done this way for strain relief. 
![Cable Routing Photo](./images/interface_wire_routing.JPG)

After assembly, tune the trim pots so that the MIC input for the IC-705 reaches 50 mVpp while calling CQ.

I set the device side mic to 45 mVpp as this was saturation for my device. To tune it yourself, use the record function to save a CQ TX to the microSD card, then play it back with AF GAIN = 30. You should get full scale output without clipping and use that to tune the trim pot. Pair with any free app or built-in program that shows the mic level input on your device.

At the time of writing, the total component cost was $14.50 and the PCB was $1.20/ea. I have 8 extra PCBs so create an issue here to contact me about getting one. Send me a self-addressed stamped envelope and I'll send you one for free.


## IC-705 and iOS Settings

IC-705:
- MIC gain = 50% (default)
- COMP = 5 (default)
- VOX gain = 50% (default)
- ANTI VOX = 50% (default)
- Delay = 0.0s
- Voice delay = OFF (default)
- VOX = on
- AF GAIN = 30
- Frequency = as appropriate (14.074, etc) 
- Mode = USB (do NOT use USB-D, VOX doesn't work)

iOS:
- Put the phone to silent mode. ALL sounds will make its way to the radio, so silent mode enabled to prevent your TX from getting in trouble when you get a notification.
- Turn the volume all the way up (which should have been where it was when you tuned the trim pots).
