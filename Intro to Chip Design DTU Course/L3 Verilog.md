--- 

[[L2 The Transistor, Inverter, and CMOS Gates]]

[[Verilog Cheat Sheet]]

---

+ Verilog is an industry standard
	+ System Verilog is an extension mainly used for verification
		+ E.g. object oriented programming
	+ Verilog syntax is very similar to C
		+ Module based, ports, wires and regs

# Verilog Assignments

+ Blocking assignments ( = )
	+ Used for sequential execution (order matters)
		+ Combinational logic
+ Non-blocking assignments ( <= )
	+ Used for parallel execution
		+ Sequential logic

# Reg VS Wire

+ Reg used in `always` blocks
	+ Can be a wire or register
		+ Dependent on usage
+ Wire is a wire
	+ Represents a port or combinational logic
	+ Can be assigned and connects modules

---

[[Lab 3 Verilog and Testing]]

[[L4 PnR Tools]]
