# 27C322/160/800/400 Programming Adapter for the TL866 and T48

This adapter allows you to program the 27C322, 27C160, 27C800, and 27C400 chips on your MiniPro TL866 programmer. The 27C322/160/800 EPROMs are 42 pins wide, but the TL866 and TL866II programmer only has a 40-pin socket. If you choose the 27C4096 EPROM (4 Mbit) on the IC selection list, you can then use the on-board switches to manually switch upper address pins A18 to A20. This allows you to fill up a larger chip (27C322 has a storage space of 32 Mbit) in 4 Mbit chunks.

YES, these work with the T48 as well!

![image](https://user-images.githubusercontent.com/97127539/171543863-88d8a002-ac89-4fb8-887f-86cff1280b19.png)

# Bill of Materials

Resistor wattage can be 1/10 W or larger.

| Reference Designator  | Part Number/Value | Footprint  |
| ------------- | ------------- | ------------- |
| R18  | 10 kΩ | 0805  |
| R19  | 10 kΩ | 0805  |
| R20  | 10 kΩ | 0805  |
| RE  | 10 kΩ | 0805  |
| RB  | 330 Ω | 0805  |
| Q1  | 2N3906 | SOT-23  |
| S18-S20  | <a href="https://www.digikey.com/short/tp5cw74d">DIP Switch x3, SPST</a> | --  |
| S1  | <a href="https://www.digikey.com/short/2q237b7r">Slide Switch, DPDT</a> | --  |
| xx  | <a href="https://www.ebay.com/sch/i.html?_from=R40&_trksid=p2380057.m570.l1313&_nkw=dip+42+socket&_sacat=0">Wide 42-pin DIP Socket</a> | DIP-42  |
| xx  | <a href="https://www.ebay.com/sch/i.html?_from=R40&_trksid=p2334524.m570.l1313&_nkw=40-pin+male+header+0.1%22&_sacat=0&LH_TitleDesc=0&_odkw=0.1%22+header+pins&_osacat=0">Male Header Pins</a> | 2x rows of 18-pin  |

# How to Use the Adapter

Load up the 27C4096 chip on the TL866 software, and load up the first chunk from your original file. Change the VPP to 12.5 V, as this is dictated for programming voltage in the datasheet. Then, uncheck the “Check ID” option. Note that if you have the new TL866II and are using “Xgpro” software, you need to uncheck “Pin Detect” as well. Your window should look like this:

![image](https://user-images.githubusercontent.com/97127539/171530181-9ed9c06a-898f-4c30-b795-139b50d8d6e7.png)

Here’s a table of how data is programmed into the EPROM. If A18, A19, or A20 is a “0”, that means tie it to GND, or if you’re using the adapter, put the switch in the “OFF” position. If it’s a “1”, that means tie it to VCC, or if you’re using the adapter, put the switch in the “ON” position. Program the 4 Mbit chunks in sequential order in the banks.

For the 27C322, put the small DPDT switch in 27C322 mode (to the left) and use these DIP switch positions:

![image](https://user-images.githubusercontent.com/97127539/171530207-b1ad52cb-d996-405f-9ffe-52463909d5ed.png)

For the 27C160, put the small DPDT switch in non-27C322 mode (to the right) and use these DIP switch positions:

![image](https://user-images.githubusercontent.com/97127539/171530225-da817490-f8ee-4e9b-9ff9-c50aa6f5d374.png)

For the 27C800, put the small DPDT switch in non-27C322 mode (to the right) and use these DIP switch positions:

![image](https://user-images.githubusercontent.com/97127539/171530236-a1999edc-132a-4d2b-9167-1eaa154c50d3.png)

For the 27C400, put the small DPDT switch in non-27C322 mode (to the right) and keep all DIP switch positions OFF. Also, because the 27C400 is only 40 pins, and not 42 pins, make sure to put the EPROM with pins 1 and 42 exposed.

If you get an error while programming with the MiniPro – make sure your chips are in the correct orientation, each bank is blank, you’ve unchecked the “Check ID” box, and that you’ve selected a 27C4096 EPROM from the Select IC list. Also, make sure you’ve got the DPDT switch on the adapter board on the correct option.

# How the Adapter Works

Here’s a breakdown of the pinouts on the 27C322, 27C160, and the 27C4096.

![image](https://user-images.githubusercontent.com/97127539/171531211-95199120-e55e-4d57-ac72-0ff28f971ae8.png)

Remember, the 27C4096 is the chip we’re going to tell the TL866 we’re programming. The X’s signify an address pin that goes unused on that chip. We’ll be adding three switches to manually control these pins – A18, A19, and A20. You’ll note that the 322 and 160 share every single pin, except that 160’s don’t utilize A20, and the VPP and /OE pins are a bit different.

According to the datasheet for the 27C322, pin 13 (or /GVpp as it’s defined) combines the /OE (output enable) pin and the VPP (programming voltage) pins into one. The 4096, however, keeps these pins separate. This isn’t too much of a problem, though, if we take a look at the operating modes of the 322 we can see that we never really need the /GVpp pin to be anything but zero volts or the programming voltage.

![image](https://user-images.githubusercontent.com/97127539/171531243-f5f560d3-8ca2-44c8-ab1b-33f857b3c7ff.png)

Here, VIL notates logic low, or zero volts. VIH is logic high, or anywhere between 2 and VCC + 1 volts (according to the datasheet). You’ll notice that the only mode that uses VIH is “Output Disable” which isn’t really used while programming the EPROM. So we only really need to switch pin 13 between ground and VPP (which means we can ignore pin 20 on the TL866 completely for programming the 27C322). Unfortunately, the TL866 switches VPP between the programming voltage and VCC voltage, so we need to add a bit of circuitry to change that VCC to GND.

![image](https://user-images.githubusercontent.com/97127539/171531272-329096d1-9613-4d98-937d-fd5cab2b8760.png)

The PNP transistor above conducts when the emitter voltage is greater than the base voltage. During programming, the VPP pin from the TL866 applies the high programming voltage. When this happens, current flows through the base resistor (RB), and pulls pin 13 on the 27C322 to VPP. Then, when the VPP pin on the programmer drops to VCC voltage, the base current drops and the transistor stops conducting. This causes pin 13 to be pulled to VSS through the collector resistor (RC).

Now, taking care of the extra address pins is just using simple switches with pull-down resistors.

![image](https://user-images.githubusercontent.com/97127539/171531289-113c09b3-9a74-4c50-bec4-f981d5ce90f7.png)

With this extra circuitry, we can make a standalone 27C322 adapter for the TL866. However, if you check that table again up at the top, you should’ve noticed that the 27C322 and 27C160 (and by extension, the 27C800 and 27C400) share a lot of common pins. The only real difference is which pin is used for the programming voltage – pin 32 on the 160 is the VPP pin, but on the 322 this is the A20 pin. We can easily reroute this using a double-pole-double-throw (DPDT) switch.

![image](https://user-images.githubusercontent.com/97127539/171531314-acfc17d9-4123-4cc0-bd51-bf41120d1605.png)

So with the extra circuitry, we now have our finished product, capable of allowing the TL866 to program 27C322s, 160s, 800s, and 400s.

# License

Licensed under the TAPR Open Hardware License (www.tapr.org/OHL)

©MouseBiteLabs 2022
