# VSD_TCL_PROGRAMMING_WORKSHOP



## DAY 1:  Creating a TCL command and design.csv file from UNIX shell to tcl script

## Objective

The goal of this day is to **understand how a TCL-based automation flow can process design data from an Excel/CSV input**, synthesize it using **Yosys**, and perform **timing analysis with OpenTimer** — all wrapped inside the **VSDSYNTH Toolbox**.

<img width="1607" height="881" alt="image" src="https://github.com/user-attachments/assets/e4955814-0afd-41fc-9dfd-679dad9af882" />

<img width="1597" height="805" alt="Screenshot 2025-10-30 220154" src="https://github.com/user-attachments/assets/8744067e-7b8f-4aff-b41f-c66c0380024d" />

### Scenario 1

user doesnot provide the .csv file 

#### Overview

In the VDH-FLOW synthesis automation suite, the .csv file is a required input that defines:

-> RTL source file paths, Library and constraint locations, Top module name, Synthesis configuration parameters.

If the user runs the script without providing the .csv file, the flow cannot start.
To handle this, VDH-FLOW includes a built-in check to ensure a valid configuration file is passed.

<img width="1597" height="851" alt="image" src="https://github.com/user-attachments/assets/546cddc5-50ff-428b-a65b-982ee3bd9fcb" />

### Scenario 2:
#### Overview

In this case, the user runs:

tcsh -f vdh_flow.tcl test.csv

…but the file design_config.csv is missing from the directory.
To handle this gracefully, VDH-FLOW detects the missing file, alerts the user, and exits safely.
user provides the name of .csv file but it doesnot exist 

<img width="1586" height="810" alt="Screenshot 2025-10-31 071953" src="https://github.com/user-attachments/assets/00d4197d-bd7d-4bb2-9f95-84e4197b456d" />

### Scenario 3:
#### Overview

The --help flag provides users with guidance on:

What the .csv file should contain

How to execute the synthesis suite correctly

The purpose and structure of the tool

<img width="1739" height="911" alt="image" src="https://github.com/user-attachments/assets/6f72ab4f-b74a-4f65-bf8d-c4ddb921582c" />


This feature helps users (especially new ones) avoid mistakes like missing inputs or incorrect paths.
When --help is used, VDH-FLOW displays a clear instruction banner and exits gracefully without starting the synthesis process.
user requests for help regarding the excel sheet content and execution using --help 


## Creating CSV File
<img width="1603" height="819" alt="image" src="https://github.com/user-attachments/assets/8d36eb23-e47d-4584-8e72-f23b7c435fa3" />


Source the UNIX shell to tcl script by passing the csv file

tclsh my_synth.tcl $argv[1]

<img width="1729" height="807" alt="image" src="https://github.com/user-attachments/assets/6a60b0be-feed-46ed-8570-a51f0f748084" />

--- 

### **Day 2: Automated Design Setup and Synthesis Flow Initialization**

This stage focuses on **input preparation and validation** before synthesis.

#### Tasks Performed:
1. **Variable Extraction**  
   - Reads the design configuration CSV file.  
   - Extracts paths and parameters to dynamically create TCL-accessible variables.  
   - Example:  
     ```tcl
     set DESIGN_NAME "openMSP430"
     set NETLIST_DIR "./netlist/"
     set LIB_PATH "./library/typical.lib"
     set CONSTRAINTS_FILE "./constraints/openMSP430_design_constraints.csv"
     ```

2. **Input Validation**  
   - Validates the existence of all paths and files defined in the CSV.  
   - Checks for:
     - Netlist directory
     - Library files (.lib)
     - Constraint files (.csv)
   - Reports missing or invalid entries with clear TCL error handling.

3. **Constraint Processing**  
   - Reads CSV constraint file entry and prepares conversion logic to **SDC (Synopsys Design Constraints)** format.  
   - Creates placeholders for clock definitions and IO delay constraints.

4. **Netlist Parsing**  
   - Recursively lists all **Verilog (.v)** source files within the specified netlist directory.  
   - Produces a structured file list for Yosys synthesis.  
     ```tcl
     set VERILOG_FILES [glob ./netlist/*.v]
     ```

5. **Dynamic Script Generation**  
   - Automatically generates a **Yosys-compatible TCL synthesis script (Format-2)**.  
   - Inserts design-specific variables and synthesis steps in proper order:
     - Read libraries  
     - Read Verilog sources  
     - Elaborate design  
     - Perform synthesis  
     - Write output reports and netlists  

6. **Flow Execution**  
   - Passes the generated TCL script to **Yosys** for synthesis execution.  
   - Enables automated log capture and result summarization.

---

### **Day 3: Constraint Mapping for Yosys (openMSP430_design_constraints.csv)**

This phase automates the **conversion of CSV-based timing constraints** into **SDC** format.

#### Core Learning Goals:
- Parse and categorize data from the constraints CSV file.  
- Create clock definitions with accurate timing parameters.  
- Identify and process input/output signal constraints.  
- Automatically generate valid **SDC commands**.

#### Implementation Steps:

1. **CSV Parsing**  
   - Reads the file `openMSP430_design_constraints.csv`.  
   - Extracts constraint types such as:
     - `CLOCK`
     - `INPUT_DELAY`
     - `OUTPUT_DELAY`
     - `LATENCY`

2. **Clock Definition Extraction**
   - Identifies clock source signals and their specifications:  
     - Period  
     - Duty cycle  
     - Latency  
   - Generates SDC entries such as:  
     ```tcl
     create_clock -name CLK -period 10 [get_ports clk]
     set_clock_latency -source 1.2 [get_clocks CLK]
     ```

3. **Bus Signal Detection (Regex-based)**  
   - Parses Verilog netlists to detect bus structures like:
     ```verilog
     input [7:0] data_in;
     ```
   - Differentiates between **single-bit** and **bus-level** signals.

4. **Input Delay Calculation**  
   - Maps CSV fields to `set_input_delay` commands automatically.  
   - Example:
     ```tcl
     set_input_delay 2.5 -clock [get_clocks CLK] [get_ports {data_in[*]}]
     ```

5. **Output Delay and Latency**  
   - Generates `set_output_delay` and latency constraints for accurate timing modeling.

6. **SDC Generation Output**  
   - Final SDC file example:
     ```
     create_clock -name CLK -period 10 [get_ports clk]
     set_input_delay 2.5 -clock CLK [get_ports {data_in[*]}]
     set_output_delay 3.0 -clock CLK [get_ports {data_out[*]}]
     set_clock_latency -source 1.2 [get_clocks CLK]
     ```

---
### Author

- Karthiswaran R
- B.E Electronics Engineering (VLSIDT)
- K. S. Rangasamy College of Technology

---
## “Automation is not just about reducing effort — it’s about building reliability, speed, and reproducibility into silicon design.”
