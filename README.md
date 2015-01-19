# Part A. 1K Bit SRAM Schematic Design

### a) Prime Specs
This is a 1024-bit SRAM with four 256-bit banks. <br />
1. Address bits are [A5:A0], I used the MSB four bits which are A5A4A3A2 for word line selecting, and LSB two bits which are A1A0 for bank selecting. <br />
2. To save area in layout design and to reduce the wire delay, I did not arrange the bit lines consecutively (i.e. BL0, BL1, BL2 … BL63). Instead, I put together BLs which are suppose to converge into the same column MUX (i.e. BL0, BL16, BL32, BL48, BL1, BL17,ect).<br />
3. I put sense amplifier after the MUX, so I need only 16 SAs instead of 64.<br />
4. To ensure fast charging and discharging of the long word lines, I add an dedicated 4x inverter before each bank. Hence the output of row decoder are ~WLs instead of WL.<br />
5. I used two level column MUX, which is 4-to-1, and I combined the read and write MUX together using all NMOSs and all PMOSs. Hence the column decoder can be saved. Since the MUXs are put before the sense amplifier, the read MUX is all PMOSs and carefully sized so that it can pass delta V as low as 200mv within a short time.<br />
<b>Sizing detail:</b><br />
SRAM cell: nMOS pulldowns: 600n/200n; pMOS pullups: 400n/300n; Access transistors: 400n /200n;<br />
Write circuit: <br />
Sense amplifier: <br />
Precharge circuit:<br />
Row decoder: 4X NANDs(), 4X NORs, 4X inverters<br />
Output register:<br />
Column MUX: NMOSs: 300n/200n; PMOSs: 300n/200n.<br />
<b>Signals:</b> <br />
![image] (https://dl.dropboxusercontent.com/s/nr1vl78u6cdk6xc/SRAMsignalTable.PNG?dl=0)

### b) Schematic

##### SRAM cell

![image] (https://dl.dropboxusercontent.com/s/hdzzways2kh1w07/SRAMcell.png?dl=0)

### Sense amplifier
![image] (https://dl.dropboxusercontent.com/s/r4xsxuylwn9390s/image2.png?dl=0)
##### Write circuit
![image] (https://dl.dropboxusercontent.com/s/oqs5l6ow43q5s86/image3.png?dl=0)
##### Read circuit
![image] (https://dl.dropboxusercontent.com/s/vho4gxly2adp5tx/image4.png?dl=0)
##### Precharge circuit
![image] (https://dl.dropboxusercontent.com/s/ff8cwtcxmamiu9x/image5.png?dl=0)
##### One bank schematic
![image] (https://dl.dropboxusercontent.com/s/jttw2rh6zct8lit/image6.png?dl=0)
##### Overall schematic
![image] (https://dl.dropboxusercontent.com/s/gcfeydgpeolc5yp/image7.png?dl=0)
##### Row decoder
![image] (https://dl.dropboxusercontent.com/s/2idovgrzuwxghg9/image8.png?dl=0)
##### Column mux
![image] (https://dl.dropboxusercontent.com/s/0rw29xzvj4lymo3/image9.png?dl=0)




### c) Single cell test
<b>Input vector file:<\b>
```
radix	1	1   1   1   1   1
io		i	i   i	i   i	i 
vname	data ~data read_en write_en precharge_en ~WL 
slope	0.01
vih		1.8
tunit	400ps

0		1 0 1 0 0 1
4		1 0 1 1 1 0
8		1 0 1 1 1 1
12		1 0 0 0 0 1
16		1 0 0 0 1 1
20		1 0 0 0 1 0
24		1 0 0 0 1 1
28      1 0 1 1 0 1
32		0 1 1 0 0 1
36		0 1 1 1 1 0
40		0 1 1 1 1 1
44		0 1 0 0 0 1
48		0 1 0 0 1 1
52		0 1 0 0 1 0
56		0 1 0 0 1 1
60      0 1 1 1 0 1
```
<b>Waveform:<\b>
![image] (https://dl.dropboxusercontent.com/s/dl107s6xujsgdbq/image11.png?dl=0

### d) Functional test for consecutive read and write operations

Suppose,<br />
write order: 1 2 3 4 5 6 7 8 9 0;<br />
read order: 1 1 7 5 8 6 0 1 5 1.<br />

<b>Input vector file:<\b>
```
radix	4	2   4	2   4	4   4	4   4   4   4   4   1   1   1   1   1   1
io		i	i   i	i   i	i   i	i   i	i   i   i   i   i   i   i   i   i
vname	A[5:2] A[1:0] ~A[5:2] ~A[1:0] data[15:12] data[11:8] data[7:4] data[3:0] ~data[15:12] ~data[11:8] ~data[7:4] ~data[3:0] read_en write_en precharge_en wl_en clk ~clk

slope	0.01
vih		1.8
tunit	400ps


0		5 1 A 2 9 B D 5 6 4 2 A 1 0 1 0 0 1
4		5 1 A 2 9 B D 5 6 4 2 A 1 0 0 0 0 1
8		5 1 A 2 9 B D 5 6 4 2 A 1 1 1 1 0 1
12		5 1 A 2 9 B D 5 6 4 2 A 1 1 1 0 0 1
16		D 2 2 1 7 7 1 3 8 8 E C 1 0 0 0 0 1
20		D 2 2 1 7 7 1 3 8 8 E C 1 1 1 1 0 1
24		D 2 2 1 7 7 1 3 8 8 E C 1 1 1 0 0 1
28		5 1 A 2 4 C 5 6 B 3 A 9 1 0 0 0 0 1
32		5 1 A 2 4 C 5 6 B 3 A 9 1 1 1 1 0 1
36		5 1 A 2 4 C 5 6 B 3 A 9 1 1 1 0 0 1
40		C 3 3 0 E 1 5 C 1 E A 3 1 0 0 0 0 1
44		C 3 3 0 E 1 5 C 1 E A 3 1 1 1 1 0 1
48		C 3 3 0 E 1 5 C 1 E A 3 1 1 1 0 0 1
52		F 3 0 0 B A 6 C 4 5 9 3 1 0 0 0 0 1
56		F 3 0 0 B A 6 C 4 5 9 3 1 1 1 1 0 1
60      F 3 0 0 B A 6 C 4 5 9 3 1 1 1 0 0 1
64      2 2 D 1 9 F 4 4 6 0 B B 1 0 0 0 0 1
68      2 2 D 1 9 F 4 4 6 0 B B 1 1 1 1 0 1
72      2 2 D 1 9 F 4 4 6 0 B B 1 1 1 0 0 1
76      9 3 6 0 F F 8 3 0 0 7 C 1 0 0 0 0 1
80      9 3 6 0 F F 8 3 0 0 7 C 1 1 1 1 0 1
84      9 3 6 0 F F 8 3 0 0 7 C 1 1 1 0 1 0
88      A 0 5 3 1 8 D 7 E 7 2 8 1 0 0 0 1 0
92      A 0 5 3 1 8 D 7 E 7 2 8 1 1 1 1 0 1
96      A 0 5 3 1 8 D 7 E 7 2 8 1 1 1 0 0 1
100     6 1 9 2 1 A 2 B E 5 D 4 1 0 0 0 1 0
104     6 1 9 2 1 A 2 B E 5 D 4 1 1 1 1 1 0
108     6 1 9 2 1 A 2 B E 5 D 4 1 1 1 0 0 1
112     2 3 D 0 8 0 F E 7 F 0 1 1 0 0 0 0 1
116     2 3 D 0 8 0 F E 7 F 0 1 1 1 1 1 1 0 
120     2 3 D 0 8 0 F E 7 F 0 1 1 1 1 0 1 0
124		5 1 A 2 1 1 1 1 F F F F 1 0 0 0 0 1
128		5 1 A 2 1 1 1 1 F F F F 0 0 0 0 0 1
132		5 1 A 2 1 1 1 1 F F F F 0 0 1 1 1 0
136		5 1 A 2 1 1 1 1 F F F F 0 0 1 0 1 0
140     5 1 A 2 1 1 1 1 F F F F 1 0 0 0 0 1
144		5 1 A 2 1 1 1 1 F F F F 0 0 0 0 0 1
148		5 1 A 2 1 1 1 1 F F F F 0 0 1 1 1 0
152		5 1 A 2 1 1 1 1 F F F F 0 0 1 0 1 0
156		9 3 6 0 1 1 1 1 F F F F 1 0 0 0 0 1
160  	9 3 6 0 1 1 1 1 F F F F 0 0 0 0 0 1
164		9 3 6 0 1 1 1 1 F F F F 0 0 1 1 1 0
168  	9 3 6 0 1 1 1 1 F F F F 0 0 1 0 1 0
172		F 3 0 0 1 1 1 1 F F F F 1 0 0 0 0 1
176		F 3 0 0 1 1 1 1 F F F F 0 0 0 0 0 1
180		F 3 0 0 1 1 1 1 F F F F 0 0 1 1 1 0
184		F 3 0 0 1 1 1 1 F F F F 0 0 1 0 1 0
188     A 0 5 3 1 1 1 1 F F F F 1 0 0 0 0 1
192		A 0 5 3 1 1 1 1 F F F F 0 0 0 0 0 1
196		A 0 5 3 1 1 1 1 F F F F 0 0 1 1 1 0
200		A 0 5 3 1 1 1 1 F F F F 0 0 1 0 1 0
204		2 2 D 1 1 1 1 1 F F F F 1 0 0 0 0 1
208		2 2 D 1 1 1 1 1 F F F F 0 0 0 0 0 1
212		2 2 D 1 1 1 1 1 F F F F 0 0 1 1 1 0
216		2 2 D 1 1 1 1 1 F F F F 0 0 1 0 1 0
220     2 3 D 0 1 1 1 1 F F F F 1 0 0 0 0 1
224		2 3 D 0 1 1 1 1 F F F F 0 0 0 0 0 1
228		2 3 D 0 1 1 1 1 F F F F 0 0 1 1 1 0
232		2 3 D 0 1 1 1 1 F F F F 0 0 1 0 1 0
236		5 1 A 2 1 1 1 1 F F F F 1 0 0 0 0 1
240 	5 1 A 2 1 1 1 1 F F F F 0 0 0 0 0 1
244		5 1 A 2 1 1 1 1 F F F F 0 0 1 1 1 0
248 	5 1 A 2 1 1 1 1 F F F F 0 0 1 0 1 0
252 	F 3 0 0 1 1 1 1 F F F F 1 0 0 0 0 1
256 	F 3 0 0 1 1 1 1 F F F F 0 0 0 0 0 1
260 	F 3 0 0 1 1 1 1 F F F F 0 0 1 1 1 0
264 	F 3 0 0 1 1 1 1 F F F F 0 0 1 0 1 0
268     5 1 A 2 1 1 1 1 F F F F 1 0 0 0 0 1
272 	5 1 A 2 1 1 1 1 F F F F 0 0 0 0 0 1
276 	5 1 A 2 1 1 1 1 F F F F 0 0 1 1 1 0
280 	5 1 A 2 1 1 1 1 F F F F 0 0 1 0 1 0
```

<b>Waveform:<\b>
![image] (https://dl.dropboxusercontent.com/s/9znj2mapsp47uzm/image15.png?dl=0)

# Part B. 1K Bit SRAM Layout Design

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



