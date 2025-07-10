This cheatsheet is for working with OpenLane2 on macOS using the terminal. It includes file navigation, design setup, and running OpenLane flows.

---
## 🚀 Running OpenLane2

### 1. Enter the OpenLane Shell

From your terminal, go to the OpenLane2 directory:

```
cd ~/openlane2
nix-shell --pure shell.nix
```

> This gives you access to the OpenLane CLI.

### 2. Create a Design Directory

```
mkdir -p ~/my_designs/pm32
cd ~/my_designs/pm32
```

### 3. Add Required Files

#### RTL File Example (`top.v`)

```
module top (
  input logic clk,
  input logic rst,
  output logic [3:0] out
);
  always_ff @(posedge clk or posedge rst)
    if (rst) out <= 0;
    else     out <= out + 1;
endmodule
```

#### Config File Example (`config.json`)

```
{

"DESIGN_NAME": "pm32",

"VERILOG_FILES": ["dir::pm32.v", "dir::spm.v"],

"CLOCK_PERIOD": 25,

"CLOCK_PORT": "clk",

"FP_PIN_ORDER_CFG": "dir::pin_order.cfg"

}
```

A config file needs 4 variables at minimum:
1. Design_name
2. Verilog_files
3. Clock_period
4. Clock_port

---

## 🧪 Running Your Design

### 1. Navigate to Your Design Directory (optional)

```
cd ~/my_designs/pm32
```

### 2. Run Full Flow

```
openlane ~/my_designs/pm32/config.json
```

### 3. Checking the results (klayout)

```
openlane --last-run --flow openinklayout ~/my_designs/pm32/config.json
```

### 4. Checking the results (openlane gui)

```
openlane --last-run --flow openinopenroad ~/my_designs/pm32/config.json
```

---

## 📂 Output and Results

After running, OpenLane will generate a `runs` directory with your results inside the working project folder.

Inside you'll find logs, reports, and layout files (GDSII, LEF, DEF, etc.).

The results and logs of DRC, LVS, STA, and antenna analysis will be found here as well.

---

## 📁 File and Directory Commands

### Create a Directory

```
mkdir -p ~/my_designs/pm32
```

### Go to a Directory

```
cd ~/my_designs/pm32
```

### List Files

```
ls
```

### Open a Folder in Finder

```
open .
```

---

## 📄 Create and Edit Files

### Create an Empty File

```
touch top.v
touch config.json
touch ~/my_designs/pm32/top.v
```

### Add Text to a File

```
echo "module top(); endmodule" > top.v
```

### Create a Multi-line File

```
cat > top.v
module top;
  // your design
endmodule
<Ctrl+D to save and exit>
```

---

## 🧠 Useful Tips

- Use `pwd` to print your current working directory.
- Always check `config.json` for correct port names and clock.
- Default standard cell library is `sky130_fd_sc_hd`.

---
