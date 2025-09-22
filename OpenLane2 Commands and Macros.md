# Config variables
## **Floorplanning Variables**

Floorplanning is the first step in the physical design process and is critical for a successful layout. These variables control the size, shape, and placement of your design's core and macros.

### **Core Area and Utilization**

- `DIE_AREA`: This defines the total chip area. It's a list of four coordinates: `[x1, y1, x2, y2]`. For example, `"0 0 1000 1000"` defines a 1000x1000 µm die.
    
- `FP_CORE_UTIL`: This is the target **utilization** of the core area, as a percentage. A value of `45` means that 45% of the core area will be occupied by standard cells. A lower utilization can help with routing congestion.
    
- `FP_ASPECT_RATIO`: This sets the desired **aspect ratio** of the core area (height/width). A value of `1.0` creates a square core.
    
- `FP_SIZING`: This can be `absolute` or `relative`. If `absolute`, you must define `DIE_AREA`. If `relative`, OpenLane will determine the die area based on your design size and `FP_CORE_UTIL`.
    

### **Macro Placement**

- `MACRO_PLACEMENT_CFG`: This points to a file that specifies the **placement** of macros. The file format is simple:
    
    ```
    instance_name x_position y_position orientation
    ```
    
    For example: `my_macro 100 150 N`
    
- `FP_MACRO_CHANNEL`: This defines the **channel width** between macros, specified as a list of horizontal and vertical channel widths.
    

### **Pin Placement**

- `FP_PIN_ORDER_CFG`: This points to a file that defines the **order of pins** on each side of the chip. This is important for ensuring that your chip can be connected to the package. The format is a list of pins for each side (`top`, `bottom`, `left`, `right`).
    

---

## **Power Distribution Network (PDN) Variables**

A robust PDN is essential for reliable chip operation. These variables allow you to create a custom power grid.

### **Straps**

- `FP_PDN_VOFFSET`, `FP_PDN_HOFFSET`: These set the **offset** of the first vertical and horizontal straps from the core boundary.
    
- `FP_PDN_VWIDTH`, `FP_PDN_HWIDTH`: These define the **width** of the vertical and horizontal straps.
    
- `FP_PDN_VSPACING`, `FP_PDN_HSPACING`: These control the **spacing** between the straps.
    
- `FP_PDN_VPITCH`, `FP_PDN_HPITCH`: These set the **pitch** of the straps. The pitch is the distance from the start of one strap to the start of the next.
    

### **Core Ring**

- `FP_PDN_CORE_RING`: A boolean that, when `true`, creates a **power and ground ring** around the core of the design.
    
- `FP_PDN_CORE_RING_VWIDTH`, `FP_PDN_CORE_RING_HWIDTH`: The **width** of the vertical and horizontal components of the core ring.
    
- `FP_PDN_CORE_RING_VOFFSET`, `FP_PDN_CORE_RING_HOFFSET`: The **offset** of the core ring from the die boundaries.
    

---

## **Clock Tree Synthesis (CTS) Variables**

CTS builds the clock network, and these variables help you control the process to meet your timing goals.

### **Clock Definition**

- `CLOCK_PORT`: The name of the **primary clock port** in your design.
    
- `CLOCK_PERIOD`: The **clock period** in nanoseconds.
    
- `CLOCK_UNCERTAINTY`: An estimate of the clock **uncertainty**, which is used by the timing analyzer.
    
- `CLOCK_TRANSITION`: The desired maximum **transition time** for the clock signal.
    

### **CTS Tuning**

- `CTS_TARGET_SKEW`: The maximum allowable **skew** between any two clock endpoints.
    
- `CTS_CLK_BUFFER_LIST`: A list of **buffers** that the CTS tool can use to build the clock tree. You can specify different buffer strengths to fine-tune the clock tree. For example: `"sky130_fd_sc_hd__buf_1 sky130_fd_sc_hd__buf_2"`
    
- `CTS_SINK_CLUSTERING_SIZE`, `CTS_SINK_CLUSTERING_CAP`: These variables control how sinks are clustered during CTS. This can impact the final skew and power consumption.
    

---

# **Tcl Scripting for Advanced Customization**

While the JSON configuration variables provide a lot of control, you can use Tcl scripts for even more advanced customization. OpenLane 2 allows you to hook Tcl scripts into various stages of the flow.

### **How it Works**

You can specify a Tcl script to be run before or after any step in the flow. This is done in your `config.json` file. For example, to run a custom Tcl script after floorplanning, you would add this to your configuration:

JSON

```
{
    "POST_FLOORPLAN_SCRIPT": "dir::my_floorplan_script.tcl"
}
```

### **Example Tcl Scripts**

Here are a few examples of what you can do with Tcl scripts:

- **Custom Floorplan:** You can use Tcl to create a more complex floorplan than what is possible with the standard variables. This could involve creating non-rectangular core areas or placing macros in specific relative positions.
    
    Tcl
    
    ```
    # my_floorplan_script.tcl
    # (This is a simplified example)
    # Get the placement of a macro
    set macro_loc [get_property [get_cells my_macro] LOC]
    # Place another macro relative to the first
    place_cell another_macro [expr [lindex $macro_loc 0] + 100] [lindex $macro_loc 1]
    ```
    
- **Custom PDN:** You can create a more complex PDN, such as a multi-level PDN with different strap configurations for different parts of the chip.
    
    Tcl
    
    ```
    # my_pdn_script.tcl
    # (This is a simplified example)
    # Create a custom power stripe
    add_pdn_stripe -layer met5 -width 2.0 -pitch 10.0 -offset 5.0 -follow_core_ring
    ```
    
- **Custom CTS:** You can create custom clock routing rules or define specific clock tree structures.
    
    Tcl
    
    ```
    # my_cts_script.tcl
    # (This is a simplified example)
    # Set a don't-touch on a specific clock net
    set_dont_touch [get_nets clk_gate]
    ```
    

---
### 1. Can you treat your verilog module as a macro (Ex. `ALU`)?

Yes, absolutely, but with one important condition: a "macro" in the physical design world isn't just a Verilog module. **A macro is a block that has a pre-defined physical layout.**

- **If your `ALU` is just RTL (Verilog code):** OpenLane will synthesize it into a collection of standard cells (ANDs, ORs, FLIP-FLOPs, etc.) and place them automatically. You cannot "place" the `ALU` module itself in this case, only the individual standard cells it becomes.
    
- **If your `ALU` is a hardened block:** This means you have already created a physical layout for it (usually from a separate synthesis and place-and-route run). This process generates a **LEF file**, which describes the macro's physical dimensions, pin locations, and blockages.
    

**To treat your `ALU` as a macro, you must have its LEF file.** Assuming you have `ALU.lef`, you tell OpenLane about it in your `config.json`:

JSON

```
{
    "DESIGN_NAME": "my_chip",
    "VERILOG_FILES": "dir::src/*.v",
    "...other variables...": "...",

    "EXTRA_LEFS": [
        "dir::macros/ALU.lef"
    ]
}
```

This `EXTRA_LEFS` variable tells OpenLane to load your ALU's physical model so the placer knows its size and shape.

### 2. How do you execute the Tcl script in the workflow?

You are exactly right—you add an entry to your `config.json` file. OpenLane 2 is designed with "hooks" that allow you to run custom Tcl scripts at various points in the flow.

For macro placement, the best time to run your script is right after the floorplan has been initialized but before the standard cells are placed. The perfect hook for this is `POST_FLOORPLAN_SCRIPT`.

---

## **Putting It All Together: A Complete Example**

Let's say your project has the following file structure:

```
my_design/
├── config.json
├── src/
│   ├── my_chip.v
│   └── ALU.v
├── macros/
│   └── ALU.lef
└── scripts/
    └── custom_placement.tcl
```

#### **Step 1: Create Your Custom Tcl Script**

Create the file `scripts/custom_placement.tcl`. This script will contain the OpenROAD commands to place your ALU. Let's assume the instance of your `ALU` in `my_chip.v` is named `alu_instance`.

**`scripts/custom_placement.tcl`:**

Tcl

```
# This script will be executed after the initial floorplan is created.

puts "## Running custom placement script... ##"

# Manually place the ALU instance at coordinates (200, 200)
# The placer will honor this as a fixed placement.
place_macro u_alu_instance -loc {200 200} -orient N -snap_to_grid

puts "## Custom placement of ALU complete. ##"
```

#### **Step 2: Update Your `config.json`**

Now, edit your `config.json` to tell OpenLane two things:

1. Load the `ALU.lef` file as an extra LEF.
    
2. Execute your `custom_placement.tcl` script after the floorplanning step.
    

**`config.json`:**

JSON

```
{
    "DESIGN_NAME": "my_chip",
    "VERILOG_FILES": "dir::src/*.v",
    "CLOCK_PORT": "clk",
    "CLOCK_PERIOD": 10.0,

    "FP_SIZING": "relative",
    "FP_CORE_UTIL": 50,

    // 1. Tell OpenLane about the ALU's physical layout
    "EXTRA_LEFS": [
        "dir::macros/ALU.lef"
    ],

    // 2. Tell OpenLane to run your Tcl script at the right time
    "POST_FLOORPLAN_SCRIPT": "dir::scripts/custom_placement.tcl"
}
```

That's it! When you run OpenLane, it will proceed as normal, but when it reaches the end of the `Floorplan` step, it will automatically execute your `custom_placement.tcl` script. Your `u_alu_instance` will be locked into the position you specified, and the rest of the flow will continue around it.

---
## TCL Script Commands

## **`rtl_macro_placer`**

This is the main workhorse command that triggers the **automated macro placement engine**. You run this command after you have loaded your design and defined your floorplan. The tool then uses its hierarchical, data-flow-driven algorithm to find the best locations for all the macros.

The behavior of this command is controlled by a wide range of options that let you define physical rules and prioritize different goals.

#### **Key Options**

- **`-halo {width height}`**: Defines a **keep-out area** around each macro. For example, `-halo {2 3}` adds a 2µm halo on the left and right sides and a 3µm halo on the top and bottom. This prevents standard cells from being placed too close to the macro, which can help with pin access and reduce routing congestion around the macro's edges.
    
- **`-fence {macro_name lx ly ux uy}`**: Creates a **hard constraint**, forcing a specific macro to be placed _inside_ the defined rectangular region. The coordinates `lx ly` are the lower-left corner and `ux uy` are the upper-right corner. This is useful for macros that must be in a certain quadrant of the chip, for example.
    
- **`-hor_layer <layer_name>`** and **`-ver_layer <layer_name>`**: Specifies the preferred metal layers for horizontal and vertical routing. The placer uses this information to understand where routing resources are most available, which can influence where it places macros to ensure they can be connected easily.
    
- **Weight Options (`-*_wt`)**: These are the most important options for tuning the placer's behavior. They are floating-point values (usually between 0.0 and 1.0) that tell the simulated annealing algorithm what to prioritize. The placer tries to minimize a "cost" that is a weighted sum of these factors.
    
    - **`-wirelen_wt <value>`**: Controls the importance of **minimizing wirelength**. This is often the highest priority, as shorter wires lead to better performance and lower power. A higher weight makes the tool focus intensely on placing connected macros close together.
        
    - **`-area_wt <value>`**: Controls the importance of achieving a **compact placement** that minimizes the total area of the floorplan.
        
    - **`-guidance_wt <value>`**: Controls how strongly the placer should follow any hints you provided with the `set_macro_guidance_region` command. If this is 0, guidance regions are ignored.
        
    - **`-boundary_wt <value>`**: Penalizes placing macros close to the core boundary. This can be useful to reserve the periphery for standard cells.
        
    - **`-macro_blockage_wt <value>`**: Penalizes placing macros near other macros. This can help spread macros out to avoid creating large areas of routing congestion.
        

---

## **`place_macro`**

This command is used for **manual placement**. It allows you to bypass the automated engine and place a single, specific macro at an exact location and orientation. This is a "hard" placement; the placer will not move this macro unless you unplace it.

This is typically used for critical macros whose location is predetermined by system-level constraints, like I/O-related macros that need to be near the chip edge.

#### **Syntax and Options**

`place_macro <macro_name> -loc {x y} -orient <orientation> [options]`

- **`<macro_name>`**: The instance name of the macro you want to place (e.g., `u_my_sram`).
    
- **`-loc {x y}`**: The **exact coordinates** for the macro's bottom-left corner.
    
- **`-orient <orientation>`**: The desired orientation. Common values include:
    
    - **`N`**: North (normal orientation)
        
    - **`S`**: South (rotated 180 degrees)
        
    - **`FN`**: Flipped North (mirrored vertically)
        
    - **`FS`**: Flipped South (mirrored vertically, then rotated 180 degrees)
        
- **`-snap_to_grid`**: An optional flag that will adjust the coordinates you provided in `-loc` to the nearest legal site on the placement grid. This is highly recommended to ensure the placement is valid.
    
- **`-allow_overlap`**: A dangerous but sometimes useful flag for debugging that allows the macro to be placed on top of other macros or blockages. In a normal flow, this should not be used.
    

---

## **`set_macro_guidance_region`**

This command acts as a **suggestion** or **hint** for the automated placer. Unlike `place_macro` or a `-fence`, it doesn't force the macro into a location. Instead, it tells the `rtl_macro_placer` engine, "It would be good if you could place this macro somewhere inside this box."

This command does nothing by itself. It simply defines a region that will be considered by `rtl_macro_placer` if the **`-guidance_wt`** option is set to a value greater than 0.

#### **Syntax and Options**

`set_macro_guidance_region <macro_name> -region {lx ly ux uy}`

- **`<macro_name>`**: The instance name of the macro you want to guide.
    
- **`-region {lx ly ux uy}`**: The coordinates for the **bottom-left (`lx ly`)** and **upper-right (`ux uy`)** corners of the suggested rectangular area.
    

You would use this when you want to influence the placement without strictly constraining it, allowing the placer to make a final trade-off between following your hint and optimizing for wirelength.

---
# Macros VS Flat Flow

Synthesizing each module individually is a powerful technique called a **hierarchical** or **bottom-up** design flow, but it's often more complex than the standard **flat** flow. The best approach depends on the size of your design and your specific goals.

Let's compare the two methods.

---

### **Method 1: The Standard "Flat" Flow 

In a flat flow, you provide **all** your Verilog files (for the CPU, ALU, registers, etc.) to the tool at once.

- **How it works:** The synthesis tool looks at the entire design as one big problem. It performs optimizations across the boundaries of your modules, potentially merging logic or sharing resources to get the best overall result.1 During placement, the tool places all the individual standard cells from all modules together in one large "sea."
    
- **Analogy:** It's like giving an architect a complete blueprint for a house and having them build everything on-site from raw materials.
    

#### **Pros:**

- **Simpler to Manage:** You have one main script and one flow to worry about.
    
- **Best Global Optimization:** The tool has full visibility and can make the best high-level trade-offs.2
    
- **Faster for Smaller Designs:** Less overhead in setting up and managing the process.
    

#### **Cons:**

- **Less Control:** You can't easily say, "Place the entire `ALU` module in this corner." The cells that make up your ALU will be spread out wherever the placer thinks is best to minimize wirelength.
    
- **Slow on Large Designs:** For a very large CPU, synthesis and placement can take a very long time and consume a lot of memory.
    

---

### **Method 2: The "Hierarchical" (Bottom-Up) Flow 

This is the method you're asking about. You would treat major modules like your ALU, memory controller, or floating-point unit as separate, smaller chips.

- **How it works:** You run each module through its own complete synthesis and place-and-route flow. This process "hardens" the module, producing a physical **macro** with a defined shape, pin locations (`.lef` file), and timing model (`.lib` file). In your top-level design, you then only need to place and connect these large, pre-built blocks.
    
- **Analogy:** It's like building a skyscraper using prefabricated rooms or entire floors. The final job is to lift them into place and connect the plumbing and electricity.
    

#### **Pros:**

- **Maximum Floorplan Control:** This is the biggest advantage. You get a single physical block for your ALU that you can manually place exactly where you want it using the `place_macro` command.
    
- **Manages Complexity:** Breaks a massive design problem into smaller, manageable pieces.
    
- **Enables Teamwork:** Different engineers or teams can work on hardening different blocks at the same time.
    

#### **Cons:**

- **Much More Effort:** You have to create and manage a separate, complete design flow for _each_ module you want to harden.
    
- **Timing Closure is Harder:** Optimizing each block in isolation can create problems at the top level. The connections _between_ your hardened blocks might end up being very long and slow.
    

---

### **Recommendation: What Should You Do? 

It depends on your experience level and design complexity.

#### **For Most University Projects or Medium-Sized Designs:**

**Stick with the FLAT flow.** It's much simpler and will get you to a result faster.

To get a "custom floorplan" in a flat flow, you don't place the module itself, but you can still guide the placer. You can define **placement regions** or **groups** for the standard cells belonging to a specific module. For example, you can tell the tool, "Try to place all the cells that were synthesized from the `ALU.v` module inside this rectangular area." This gives you regional control without the complexity of hardening the block.

#### **For Very Large, Complex Designs (e.g., millions of gates):**

A **HIERARCHICAL flow** is often necessary. If your CPU is extremely complex, or if one specific module (like a high-performance FPU) has very challenging timing requirements, hardening it as a separate block is a good strategy.

**In summary:** Start with a flat flow. If you find that runtimes are too long or you absolutely cannot meet performance goals without physically grouping and placing a module, _then_ consider moving to a hierarchical flow for that specific, critical block.