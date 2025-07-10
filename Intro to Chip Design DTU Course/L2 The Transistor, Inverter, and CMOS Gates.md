Information is represented with binary numbers.

> this is the foundation of digital computing and digital electronics

There are two levels in binary, which have physics representations:

+ High = logic 1
+ Low = logic 0

# The MOSFET Transistor

+ MOSFET stands for metal oxide semiconductor field effect transistor
	+ MOSFETs are good transistors because they have high input impedance and low power consumption
+ There are two types of mosfets:
	+ P-type: use holes as charge carriers, turns on when G=0
	+ N-type: use electrons as charge carriers, turns on when G=1
		+ There are 4 pins on each MOSFET transistor:
			+ Gate - controls current flow between source and drain
			+ Source- the starting point of current flow in the transistor
			+ Drain - the ending point of current flow in the transistor
			+ Bulk - the semiconductor base material
![[Pasted image 20250709205333.png]]

![[Pasted image 20250709205400.png]]

# Basic Working Principle of MOSFETs

> Voltage at gate controls a channel between source and drain, and the key mechanism is that an applied voltage at the gate creates an electric field, thereby inducing a conductive channel between source and drain

+ MOSFET regions of operation:
	+ Cutoff, V<sub>GS</sub> < V<sub>th</sub>, no current flows
	+ Linear (ohmic/triode), 