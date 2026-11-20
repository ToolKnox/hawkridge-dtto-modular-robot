# Dtto Modular Self-Reconfigurable Robot v2 - Arduino Nano Snake Module - Build Guide

https://github.com/ToolKnox/hawkridge-dtto-modular-robot

Remix of Dtto Modular Robot by Alberto Molina Perez (https://github.com/otrebla333/dtto-modular-robot, https://hackaday.io/project/9976), updated/refurbished for modern use. This guide is an adaptation shared under CC BY-SA 4.0. Print files unchanged.

## What you are building

One Dtto module (version 2.0.1). Each module is a two-part body (a "male" and a "female" half) joined by two metal-gear servos, with three hook servos and 24 magnets for coupling to other modules. Modules talk to each other over NRF24L01+ radios; the master module also carries an HC-05 Bluetooth module so you can drive the chain from a phone or PC terminal. One module can run its self-test and move its joints; snake, wheel and walker shapes need three or more modules.

The Bill of Material PDF lists the parts for **one module**. Build one module first, then multiply. Only the master module needs the HC-05.

Tools: soldering iron, thin wire, hot glue gun, small files / sandpaper, precision knife, small screwdriver, multimeter, and a 3D printer (upstream printed ABS at 0.2-0.25 mm layers with a 0.4 mm nozzle).

## 1. Firmware first

Test every Arduino Nano before it goes in; clone boards sometimes refuse to program.

1. Install the Arduino IDE and these libraries: **Adafruit NeoPixel** and **RF24** (TMRh20). Servo and SoftwareSerial come with the IDE.
2. Download the upstream sketch at the pinned commit: https://github.com/otrebla333/dtto-modular-robot/tree/b4b97069888079e5726600969b8e0783bfe2514f/Software - use `Dtto_Program_03.ino`, which matches the upstream command notes (`Short instructions for v03.txt`).
3. Near the top of the sketch:
   - `DttoType`: **0 for the master** (the module with the HC-05), **1 for every other module**.
   - `METALGEAR`: 1 when the joint servos are MG92B (as in this parts list), 0 for SG92R.
4. Board **Arduino Nano**, processor ATmega328P (try "Old Bootloader" if upload fails), upload. Upload the Blink example first if you want a quick board test.

The sketch uses: joint servos on D6 (male) and D5 (female), hook servos on D10 (base), D9 (left) and D3 (right), NRF24L01+ CE on D2 and CSN on D4 (plus the SPI pins D11/D12/D13), HC-05 on a software serial port (Nano D7 receives, D8 transmits) at 9600 baud, and the WS2812B LED on A5 (pin 19).

## 2. Print the parts (v2.0.1)

Print one set per module. Colour A and colour B are only for looks (upstream: grey and red).

| Part | File | Qty | Supports |
|---|---|---|---|
| 001-1 | 001-1(curvaext002).stl | 2 | everywhere |
| 002-1 | 002-1(curvafija002).stl | 2 | everywhere |
| 003-1 | 003-1(paredcurva003).stl | 2 | touching build plate |
| 004-1 | 004-1(interior001).stl | 2 | touching build plate |
| 005-1 | 005-1(baseM001).stl | 1 | |
| 006-1 | 006-1(baseF001).stl | 1 | |
| 007-1 | 007-1(paredAM001).stl | 1 | |
| 008-1 | 008-1(paredAF001).stl | 1 | |
| 009-1 | 009-1(ParedBM001).stl | 1 | |
| 010-1 | 010-1(paredBF001).stl | 1 | |
| 011-1 | 011-1(brazotapa002).stl | 1 | |
| 012-1 | 012-1(brazobase002).stl | 1 | everywhere |
| 013-1 | 013-1(ganchoA002).stl | 3 | |
| 014-1 | 014-1(ganchoB001).stl | 3 | |

Remove supports and sand the sliding and rotating faces; upstream notes that some parts need sanding to fit.

## 3. Mechanical assembly

1. **Centre joint.** Check that the two 004-1 parts turn freely around part 011-1. Less friction means better movement.
2. **Joint servos (MG92B) for 180 degree travel.** Open each servo, lift the board, and in series with each outer leg of the feedback potentiometer (not the middle one) solder a resistor of about 520 ohm (510 ohm works; upstream used 680 ohm and 2.2 kOhm in parallel). The board no longer fits the case: tape both sides and stick it to the side of the case. This is an irreversible servo modification.
3. Fix the two MG92B servos in the 004-1 parts with the servo screws (cut them to length).
4. **Centre the joints.** Fix the cross servo horns to parts 011-1 and 012-1 with the servo at 90 degrees (`servo.write(90)`). If the parts are not symmetric at 90 degrees, shift the horn by one spline. Check the full sweep with the Arduino "Sweep" example. Pick one half as **male** and route the female servo's cable through the centre hole to the male half, leaving slack so the joint turns freely.
5. Attach part 003-1 to 004-1 with three screws on each side.
6. **Magnets.** Glue 12 magnets into the male parts 005-1, 007-1 and 009-1 with the **north pole facing into the module**, and 12 into the female parts 006-1, 008-1 and 010-1 with the **north pole facing out**. Then two males repel and a male and a female attract. Upstream used hot glue; a builder comment warns that heat weakens neodymium magnets, so use a small dab, let the glue cool slightly first, or use CA / epoxy.
7. **Hooks.** For each SG90: cut a horn as in the upstream photos, pass it through parts 013-1 and 014-1 (they must turn freely), set the servo to 90 degrees, screw the hook to the horn, and stretch a small orthodontic elastic between the two printed parts. Fix each hook servo into 005-1, 007-1 and 009-1 with its two screws.
8. **Arduino Nano headers.** The Nano goes in without header pins, and the boards in the Bill of Material come with the headers already soldered. Desolder and remove all header pins first (solder wick or a desoldering pump, then clear the holes), then solder thin wires directly to the pads, the same way as the radios in the next step.
9. Remove the header pins from the HC-05 and NRF24L01+ and solder thin wires directly. Glue the NRF24L01+ in place clear of the moving centre part.
10. Mount 009-1 (three screws), then the base 005-1 (four screws), keeping the radio wires short enough to pass between the base servo and the top part.

All body screws are M1.7 x 4 mm self-tapping (about 40 per module).

## 4. Wiring (from the upstream schematic)

| From | To |
|---|---|
| Two 3.7 V LiPo cells in series (7.4 V) | power switch |
| Switch output | LM317 IN and Nano VIN |
| LM317 OUT (about 5.8 V) | + of all five servos |
| LM317 resistors | 330 ohm from OUT to ADJ, 1.2 kOhm from ADJ to GND |
| Nano 5V | HC-05 VCC and WS2812B VDD |
| Nano 3V3 | NRF24L01+ VCC |
| Nano D6 / D5 | male / female joint servo signal |
| Nano D10 / D9 / D3 | base / left / right hook servo signal |
| Nano D2 / D4 | NRF24L01+ CE / CSN |
| Nano D13 / D12 / D11 | NRF24L01+ SCK / MISO / MOSI |
| Nano D7 / D8 | HC-05 TX / HC-05 RX |
| Nano A5 | WS2812B DIN |
| All GND | common ground |

- Build the LM317, its two resistors and the power splice on a small perfboard no larger than about 10 x 22 mm so it fits the module. Measure the output with a multimeter before connecting servos: 1.25 V x (1 + 1200 / 330) is about 5.8 V. Upstream also suggests 100 ohm / 390 ohm (about 6.1 V).
- The HC-05 RX input is a 3.3 V logic pin; the upstream schematic wires it straight to D8. If you want to stay within its rating, put a 1 kOhm / 2 kOhm divider (from the resistor kit) between D8 and HC-05 RX.
- Mount the WS2812B on part 007-1 (trim the LED holder to fit), then fix 007-1 with two screws. Place the latching switch under that servo so it does not stick out when pressed.
- Put the two LiPo cells in the female half (better balance), route the power wires through, mount 006-1 and 008-1, glue the cells, and close with 010-1.

## 5. Batteries

The two cells are in series. Charge them **one at a time** with the 1S USB charger module (disconnect the series link first), or use a 2S balance charger if you fit a balance lead. Never charge the series pair with a 1S charger. Do not leave LiPo cells charging unattended, and stop using a puffed cell.

## 6. First run (master module)

1. Switch on. Pair your phone or PC with the HC-05 (default PIN is usually 1234 or 0000) and open a Bluetooth serial terminal at 9600 baud.
2. Commands are short text strings: the first character picks the module (`1`, `2`, ... or `a` for all), the second the action. From the upstream notes:
   - `at` - blink the RGB LEDs (test)
   - `as` - move all servos to centre
   - `arm` / `arf` - run the snake movement toward the male / female side
   - `ae` - stop everything
   - `2gb0` - move module 2's base hook to its centre (hidden) position; the last digit 1 or 2 couples or uncouples, and `r` / `l` instead of `b` pick the right / left face
   - `a` and `f` change amplitude and phase of the movement (experimental)
3. Slave modules (DttoType 1) ask the master for a number when they power up, in the order they connect.

The upstream code is described by its author as experimental and lightly commented; expect to tune centre values and amplitudes for your servos.

## Credits

Dtto Modular Robot by Alberto Molina Perez (otrebla333), Universitat Rovira i Virgili, with thanks in the original to Dr. Jose Luis Ramirez and Dr. Juan Gonzalez-Gomez. Original files, images and texts: CC BY-SA 4.0 (https://creativecommons.org/licenses/by-sa/4.0/). This guide is a rewritten adaptation (wiring table from the upstream schematic, reordered steps, added charging and safety notes) and is shared under the same CC BY-SA 4.0 licence.
