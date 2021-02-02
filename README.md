# RE:Flex Dance I/O v2 PCB Design 

This repository contains source documents and manufacturing files for the RE:Flex Dance Pad's I/O Board. It was created using [KiCAD 5.1.4](https://kicad-pcb.org/).

The I/O Boards purpose is to provide an interface between a computer (using USB 2.0) and 4 'panel boards' (using RJ45) to:
- Receive panel board pressure sensor data, organize the data and send it to computer programs
- Receive LED data from computer programs, organize and send it to panel boards to display on RGB LED array

## Getting your own

The least-work way is to order the Printed Circuit board (PCB) assembled from JLCPCB. You can also order a bare board from any PCB prototyping place, and then source and solder all the components yourself.
Board: The JLCPCB assembled route
1. Grab the latest PCB manufacturing files

    Go to the Latest release and download `release-v2-RC2.zip`. This contains all the PCB fabrication outputs from KiCAD. Alternatively, clone or download this repository, open the project from the pcb/multi-midi folder in KiCad and export the gerber files yourself.
    
    If you want, you can generate the manufacture files yourself, but this is trickier than generating the gerber files since a 3rd party script needs to be installed, and output files manually edited to work with JLCPCB. Documentation: How to generate the BOM and Centroid file from KiCAD

2. Place the order with [JLCPCB](https://jlcpcb.com)
```
    Open https://jlcpcb.com/ in your web browser.
    Click "Order Now" in the upper-right of the screen.
    Click "Add your gerber file", select the release-v2-RC2.zip file from the releases.
    Pick a PCB Qty.  It has to be a multiple of 5.  You will need 1 I/O board per platform.  Note that the per-board cost may reduce quite dramatically as you increase your order size.
    Pick a board color or leave it at the default green. Last time I checked different colors only affect build time, not price.
    Pick a surface finish or leave it on the default. The default is cheapest, but contains lead. ENIG gives the pads a nice golden finish instead of silver.
    Leave all the other options as they are.
    Scroll down to "SMT Assembly" and switch it on.  This means they will solder components on the board for you.  But not necessarily all of them.
    Select "assemble top side"
    Pick a quantity (SMT QTY).  If you picked 5 for the PCB Qty, you can choose to have only 2 assembled, to save money on parts and assembly fees.  Otherwise all of them will be assembled.
    Click "Confirm".
    On the right side of the page, click "Next"
    Click "Add BOM File"; upload the *release/bom.csv* file
    Click "Add CPL File"; upload the the *release/io-board-top-pos.csv* file
    Click "Next"
    Review the specified parts; they should all be "confirmed" in the right column, so you can leave everything as it is.
        It's IMPORTANT to check this page carefully, as stock shortages are not at all uncommon, and will be reported on this page.
        If you proceed without addressing such issues, your order will go ahead but the missing components will be left out of the assembly process.  You will not be charged for these components, but they will not be mounted on your board either.
        Also note that this page only considers a single board when giving the cost for each component.  Don't worry.  The actual per-component cost you are charged will be based on the total quantity of the component mounted on all boards combined.  You will be able to confirm this in the Bill of Materials, viewable from the shopping cart page.
    Click "Next"
        In the preview, some ICs might look like they're oriented wrong. This is a problem with their viewer; the files are correct. If it ends up wrong (not aligning with the pads), they'll either correct it or email you to find a solution.
    Click "Save to cart"
        On the shopping cart page, you can access the Bill of Material for the SMT assembly, which will show exactly what you pay for each component.
        If the total cost here is lower than you expected, take it as a sign to double-check the Bill of Material and the "Unselected Parts" in the next tab, and look for parts that should have been supplied by JLCPCB but weren't.
    Click "Check out Securely"
    Add your shipping and billing details
    Select a shipping method. Usually the DHL-based ones are fine. You can gamble on whether or not you want duty and customs paid up front (more expensive), or hope for the best with the cheaper option, in which case you might get a bill from the courier later.
    Click "Continue" when you're happy
    Click "submit order"
    Do what you need to do for payment
    Wait for your boards to get delivered
```

3. Order the remaining through-hole parts - A list of these parts can be seen at `release/unpopulated.csv`. Companies that sell parts like this include Mouser, Digikey, RS-Components and Farnell.

4. Solder the remaining parts onto the board.  For some parts, you may need to work out a way to keep them from falling out from the board when you flip it upside-down to do the soldering.  I've heard of techniques including heat-resistant tape, folded paper towels and blu-tack.  You'll work something out, but keep in mind each method has its caveats, and don't forget you're dealing with a hot soldering iron, so take care.
    Here is a guide for each through-hole part on the IO board:
    J1,J2,J3,J4 / Amphenol FCI 54602-908LF / RJ45/ethernet connector: Snaps onto the board with plastic tabs so you don't have to worry about holding it in place.  These large components can get in the way when soldering other components, so consider doing them last.
	J5 / On-Shore Technology USB-B1HSB6 / USB Type B socket: Clips onto the board, so it'll stay in place without much trouble.
	J6 / 3M 30310-6002HB / 10-pin ST-LINK connector with plastic fence: By convention, the gap in the fence goes on the side with pin 1 - the pin with the square solder pad.  The gap also faces towards the edge of the board in this instance.
    J7,J8,J9,J10,J11 / Würth Elektronik 694106301002 / 2.1mm DC barrel jack connector: Easy.  Big contacts can take a lot of solder.
    J12 / Harwin M20-9980446 / External Logic connector: It's quite possible you'll never use this connector, so you could consider postponing this one until you actually need it.  This one falls right out if not held in place, so you'll want to apply some technique as touched on above.
    SW1,SW2 / TE Connectivity 1825910-6: The pins lock into the board so you don't have to worry about it falling out of position.  When these switches are placed correctly, the pins come out on the left and right sides.  It's possible to bend the pins the wrong way if you're careless during insertion, but it's easy to rectify.

## Future Improvements
- Currently, there's a lot of connectors. We could daisy chain a number of boards if we utilized the correct UART transmission protocol. My hope is eventually to just have 3 RJ45 connectors, which can each address a maximum of 3 panels.
- We could also eliminate the need for all of the output DC power jacks by running power along the RJ45 cable, similar to 'Power over Ethernet'. This would probably depend on using lower-powered LEDs, which is already an improvement listed under the panel board.
- The RS-485 transceivers probably don't need direction-switching, and the transceiver is entirely deprecated connected to the USART clock lines. We could save some microcontroller pins by removing this. This'd also make firmware updates to panel boards over UART a lot easier.
- More output types! Currently we just have USB. In the future, we could connect to arcade machines, consoles, and other things. 
- More methods of panel addressing. Currently the RJ45 method is the only way. So any future panel boards would have to conform to that specification. Some people may just want a much easier method of addressing digital sensors or force sensitive resistors. This could be supported on the I/O board to reduce requirements for travel pads and the likes.
- Easier debugging. The status LEDs are ambiguous as to what they do. More status LEDs, more text indication on the board (e.g. 'Left Panel Unconnected' next to a status LED) could make things a lot easier for the end user.
- Future form factor updates. People might want to use these boards in an arcade pad. They may want to avoid the aluminium extrusion framework. Fitting this board for all sorts of different requirements will improve accessibility to the electronics.
- These boards could have through hole parts populated by a batch manufacturer, and be sold without the need for soldering. If you'd like to sell these boards, please get in touch, and we can help you source finished boards.

## Firmware

For the board to function, you will need to install the I/O board firmware to it. You can find this information within the repo for the IO-Firmware.

## License

For license details, see LICENSE file