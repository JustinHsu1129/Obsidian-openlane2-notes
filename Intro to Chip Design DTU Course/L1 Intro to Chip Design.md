This will be my notes for the 13 week course **Intro to Chip Design** at the Technical University of Denmark (DTU).

[Link to the Github](https://github.com/os-chip-design/chip-design-intro)

[[Lab 1 OpenLane2 set up and configuration]]

---
# Intro to chip design

- This class will introduce how to design and tape out a chip, from RTL to GDSII
- The class will use tinytapeout or efabless

# Moore's Law

* Transistor count on a chip doubles every 2 years
	* This means the power density of the chip increases exponentially

# Dennard Scaling

+ As transistor size shrinks:
	+ Voltage scales down
	+ Current scales down
	+ Power density stays constant
+ Power stays proportional to area
+ There is no more Dennard scaling due to the physical limitations on manufacturing transistors
+ Leakage current increases with smaller transistors

# Trends in Chip Design

+ Processor performance gains are slowing down
+ More specialized cores (ie. AI accelerators)

# Open Source Chip Design

+ OpenLane2
+ Skywater 130
+ RISC-V core Wildcat

# Tools (CAD)

+ Chisel
+ Verilator
+ Yosys
+ Openroad/OpenLane
+ Skywater PDK

---
# Chip Design Workflow

## Planning and RTL
1. Specification
2. Architecture design
3. RTL design
4. Verification

## Physical Design
1. Synthesis
2. Floorplan
3. Placement
4. Clock tree synthesis
5. Route
6. STA
7. Fabrication
8. Testing
9. Bring-up

---

# OpenLane

+ OpenLane is an open source digital ASIC design flow
	+ Provides a full flow from RTL to GDSII
