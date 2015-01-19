SRAM Part A. 1K Bit SRAM Schematic Design
----
### 1 Briefly explanation
This is a 1024-bit SRAM with four 256-bit banks. <br />
1. Address bits are [A5:A0], I used the MSB four bits which are A5A4A3A2 for word line selecting, and LSB two bits which are A1A0 for bank selecting. <br />
2. To save area in layout design and to reduce the wire delay, I did not arrange the bit lines consecutively (i.e. BL0, BL1, BL2 … BL63). Instead, I put together BLs which are suppose to converge into the same column MUX (i.e. BL0, BL16, BL32, BL48, BL1, BL17,ect).<br />
3. I put sense amplifier after the MUX, so I need only 16 SAs instead of 64.<br />
4. To ensure fast charging and discharging of the long word lines, I add an dedicated 4x inverter before each bank. Hence the output of row decoder are ~WLs instead of WL.<br />
5. I used two level column MUX, which is 4-to-1, and I combined the read and write MUX together using all NMOSs and all PMOSs. Hence the column decoder can be saved. Since the MUXs are put before the sense amplifier, the read MUX is all PMOSs and carefully sized so that it can pass delta V as low as 200mv within a short time.<br />
Sizing detail:<br />
SRAM cell: nMOS pulldowns: 600n/200n; pMOS pullups: 400n/300n; Access transistors: 400n /200n;<br />
Write circuit: <br />
Sense amplifier: <br />
Precharge circuit:<br />
Row decoder: 4X NANDs(), 4X NORs, 4X inverters<br />
Output register:<br />
Column MUX: NMOSs: 300n/200n; PMOSs: 300n/200n.<br />
Signals:<br />
![image] (https://dl.dropboxusercontent.com/s/nr1vl78u6cdk6xc/SRAMsignalTable.PNG?dl=0)

### 2 Schematic

##### SRAM cell

![image] (https://dl.dropboxusercontent.com/s/hdzzways2kh1w07/SRAMcell.png?dl=0)

### Sense amplifier
![image] (https://dl.dropboxusercontent.com/s/r4xsxuylwn9390s/image2.png?dl=0)
### Write circuit
![image] (https://dl.dropboxusercontent.com/s/oqs5l6ow43q5s86/image3.png?dl=0)
### Read circuit
![image] (https://dl.dropboxusercontent.com/s/vho4gxly2adp5tx/image4.png?dl=0)
### Precharge circuit
![image] (https://dl.dropboxusercontent.com/s/ff8cwtcxmamiu9x/image5.png?dl=0)
### One bank schematic
![image] (https://dl.dropboxusercontent.com/s/jttw2rh6zct8lit/image6.png?dl=0)
### Overall schematic
![image] (https://dl.dropboxusercontent.com/s/gcfeydgpeolc5yp/image7.png?dl=0)
### Row decoder
![image] (https://dl.dropboxusercontent.com/s/2idovgrzuwxghg9/image8.png?dl=0)
### Column mux
![image] (https://dl.dropboxusercontent.com/s/0rw29xzvj4lymo3/image9.png?dl=0)




c)Single cell test
Input vector file:

Waveform:

d)Functional test for consecutive read and write operations
My USC ID number: 1175 8601 51. Hence, 
write order: 1 2 3 4 5 6 7 8 9 0;
read order: 1 1 7 5 8 6 0 1 5 1.

Input vector file:


Waveform


Part B. 1K Bit SRAM Layout Design
c)Layout design 
SRAM cell

Sense amplifier

Write/read circuit

Precharge circuit,

One bank layout

Overall layout

Row decoder

Output registers (D flip flop)

Column mux.

LVS check

Functional test in the extracted view
Input vector file: 
Similar to the one used in schematic simulation, except for adding clk and ~clk
Waveform


Summary of key parameters
Area:253.3*98.5=24950.05um2

Minimal clock period:
To find the minimal clock period, I gradually reduced the tunit in the input vector file, until the result started to go wrong. The minimum clock period of this deign is 6.1ns.



