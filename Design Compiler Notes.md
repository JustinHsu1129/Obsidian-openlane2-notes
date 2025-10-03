# 1. **Design Compiler Setup & Synthesis — Summary Guide**

```markdown
# Synopsys Design Compiler (DC) Quick Start Guide

## Launching Design Compiler
- **GUI mode** (Design Vision):
  ```bash
  design_vision
```

- **CLI mode** (shell only):
    
    ```bash
    dc_shell
    ```
    
- **CLI with GUI features:**
    
    ```bash
    dc_shell -gui
    ```
    

---

## Reading in Your Verilog

```tcl
read_verilog fsm.v
```

- If multiple files:
    
    ```tcl
    read_verilog {top.v module1.v module2.v}
    ```
    

---

## Setting the Top Module

```tcl
list_designs            ;# shows all loaded modules
current_design fsm      ;# set "fsm" as the top
```

---

## Linking to Libraries

```tcl
link
```

This maps your RTL operators to real cells in the standard-cell library.

---

## Applying Constraints

- Create a clock (example: 100 MHz on port `clk`):
    
    ```tcl
    create_clock -period 10 -name clk [get_ports clk]
    ```
    
- Add input/output delays (example: 2 ns):
    
    ```tcl
    set_input_delay 2 -clock clk [all_inputs]
    set_output_delay 2 -clock clk [all_outputs]
    ```
    

---

## Running Synthesis

- Basic compile:
    
    ```tcl
    compile
    ```
    
- Recommended optimized compile:
    
    ```tcl
    compile_ultra
    ```
    

---

## Reporting Results

```tcl
report_area
report_timing
report_power
```

---

## Writing Out the Synthesized Design

- Gate-level netlist:
    
    ```tcl
    write -format verilog -hierarchy -output fsm_syn.v
    ```
    
- Internal DC database (optional):
    
    ```tcl
    write -format ddc -hierarchy -output fsm.ddc
    ```
    

---

## Exit

```tcl
exit
```

```

---

# 2. **What to Do After Generating `fsm_syn.v`**

Once you have the **synthesized netlist**, you’ve completed the **RTL → gate-level mapping** step. The flow continues like this:

### **Next steps in ASIC flow:**
1. **Functional Verification at Gate Level**
   - Run simulation (e.g., Synopsys VCS, ModelSim, or other simulators) with `fsm_syn.v` to confirm behavior matches your RTL.
   - This is called **Gate-Level Simulation (GLS)**.

2. **Static Timing Analysis (STA)**
   - Use **PrimeTime** to check if your synthesized design meets timing requirements (setup/hold).
   - DC gives preliminary timing reports, but PrimeTime is the real signoff tool.

3. **Place & Route (Physical Design)**
   - Pass `fsm_syn.v` (the netlist) and the library files into **IC Compiler II (ICC2)** or **open-source tools (e.g., OpenROAD)**.
   - This step determines the actual **chip layout** (where cells go, how wires are routed).

4. **Post-Layout Verification**
   - Extract delays from layout → run STA again.
   - Possibly re-run GLS with back-annotated timing.

5. **Final Output for Fabrication**
   - After P&R, you get a GDSII file → the mask data used for chip fabrication.

---

### **If this is for a class project** (like EEC281 or a lab):
- Usually, the expected flow stops at:  
  ✅ RTL → synthesis → timing reports → netlist  
- Optionally, you might run:  
  - **Schematic view** in Design Vision to see your FSM’s gates.  
  - **Simulation** of `fsm_syn.v` to confirm correctness.  

---

⚡ TL;DR:  
- `fsm_syn.v` is **input for the next stage** (simulation, timing analysis, or physical design).  
- For learning: run **reports** and check the schematic to understand how your FSM maps to gates.  
- For industry: you’d feed it into **PrimeTime → ICC2 → signoff flow**.

---

Do you want me to make you a **step-by-step simulation flow** (using VCS or iverilog) to check that `fsm_syn.v` actually behaves the same as your original RTL?
```