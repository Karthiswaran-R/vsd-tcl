# VSD_TCL__WORKSHOP

<img width="1597" height="805" alt="Screenshot 2025-10-30 220154" src="https://github.com/user-attachments/assets/cf06a025-ba58-4248-963c-bff3ceb570a0" />

## DAY 1: Creating a TCL Command and Passing a .csv File from UNIX Shell to TCL Script
# Objective

The objective of this session is to gain a clear understanding of how TCL-based automation flows interact with external data inputs, forming the foundation of a scalable VLSI design automation system.

In this phase, you will learn how to:

Create and execute custom TCL commands.

Pass external data (in .csv format) from the UNIX shell environment into a TCL script.

Parse and utilize the CSV data dynamically inside the script to drive design parameter configuration.

Integrate the parsed data into an EDA synthesis and analysis workflow using:

-> Yosys for logic synthesis.

-> OpenTimer for static timing analysis.

Understand how this process forms part of the VSDSYNTH Toolbox, a modular flow automation environment designed to handle multiple designs through data-driven scripting.

# Concept Overview

In a professional ASIC or FPGA design environment, engineers often need to process multiple design configurations or constraints that are stored in structured files such as CSV or Excel.
Instead of manually editing TCL scripts for each run, TCL automation enables:

-> Dynamic configuration loading.
-> Batch processing of multiple design variants.
-> Seamless integration with open-source EDA tools.

<img width="1607" height="881" alt="Screenshot 2025-10-30 220249" src="https://github.com/user-attachments/assets/ab8c8e80-c84d-4685-a540-a714f7be9f69" />

<img width="1597" height="805" alt="Screenshot 2025-10-30 220154" src="https://github.com/user-attachments/assets/37b3732e-8acd-4d0b-824b-489f64a53e76" />

### Scenario 1

user doesnot provide the .csv file 
<img width="1597" height="851" alt="Screenshot 2025-10-31 070051" src="https://github.com/user-attachments/assets/4e07035b-2412-48d8-885b-5c028efee4cb" />

### Scenario 2:
user provides the name of .csv file but it doesnot exist 
<img width="1586" height="810" alt="Screenshot 2025-10-31 071953" src="https://github.com/user-attachments/assets/1aaf413d-80f3-4ab1-86a3-a858eadb8d56" />

### Scenario 3:
user requests for help regarding the excel sheet content and execution using --help 
<img width="1739" height="911" alt="Screenshot 2025-11-02 104429" src="https://github.com/user-attachments/assets/130f9d60-1854-4e33-9d9a-ddc4e16f94d3" />

## Creating CSV File
<img width="836" height="427" alt="image" src="https://github.com/user-attachments/assets/034c654e-07bc-4852-a8d7-a0ab7e7e4168" />

Source the UNIX shell to tcl script by passing the csv file
```
tclsh my_synth.tcl $argv[1]

```

<img width="1729" height="807" alt="Screenshot 2025-11-02 212550" src="https://github.com/user-attachments/assets/7d828933-410c-415f-86c1-5fe6a8c1554d" />

### **Day 2: Automated Design Setup and Synthesis Flow Initialization**

This stage focuses on **input preparation and validation** before synthesis.

####  Tasks Performed :
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


## DAY-4 : Feeding RTL Netlist and Standard Cell Library to Yosys EDA tool for Synthesis

###  **Lab Objective**

Day 4 extends the Day 3 flow to cover **output constraints** and **integration with the Yosys synthesis tool**.
Here, we will finalize SDC generation and use it to synthesize an RTL design into a gate-level netlist.

You will:

* Add `set_output_delay` and `set_load` commands to the SDC file.
* Understand how the SDC is consumed by synthesis tools.
* Create an automated Yosys script (`synth.ys`) using TCL variables.
* Execute synthesis and verify successful gate-level netlist generation.
* Implement hierarchy checks and error-handling in TCL.

---

### 🧠 **Conceptual Background**

Synthesis is the process of converting behavioral RTL code into a structural gate-level representation using liberty models.
By feeding the generated SDC into Yosys, you ensure that synthesis honors timing constraints.
Day 4 bridges the gap between **functional design intent (RTL)** and **implementation design intent (timing)**.

### Checking the hierarchy
####  All the referenced modules are interlinked properly and the hierarchy is properly defined - Hierarchy PASS

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4a506f81-4b73-4f77-9d5e-7bc461fc1ddd" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b3e4f3c6-aa5e-4577-88c4-75851898346d" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5118afa5-324a-4823-8ce9-a5eecc3a0e70" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fbe0fb28-7f84-486a-82a7-e6090fdc3364" />

## DAY-5: Converting Yosys tool's Synthesized Gate Level Output Netlist to a Format compatible with Opentimer(Open Source EDA Tool) for Timing Analysis

### 🎯 **Lab Objective**

Day 5 is where all components come together.
You’ll automate the **entire design flow**:
CSV → SDC → Yosys → OpenTimer → QoR Report.

The focus is on:

* Creating OpenTimer configuration files (`.conf`).
* Running static timing analysis automatically.
* Parsing OpenTimer timing reports to extract QoR metrics:

  * WNS (Worst Negative Slack)
  * TNS (Total Negative Slack)
  * Setup/Hold violations
  * Instance count & runtime
* Formatting these metrics into readable tables and logs.

---


Here, the TCL script becomes a *flow controller*, capable of:

* Launching synthesis and analysis tools.
* Managing data dependencies between stages.
* Evaluating timing performance quantitatively.

**OpenTimer** performs the timing graph analysis using liberty, Verilog, and SDC inputs, applying standard STA principles:

```
Slack = Required_Time – Arrival_Time
```

A negative slack indicates a timing violation.


<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/886d0c4f-2fe5-4b09-8093-c9708187e602" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d9b845cc-4633-439a-865b-491d1372bc12" />

### Traking no of * & //// in openMSP430.synth.v

<img width="806" height="289" alt="image" src="https://github.com/user-attachments/assets/b8ce99b2-1651-41b8-bd96-75265f7ece0a" />

<img width="1847" height="354" alt="image" src="https://github.com/user-attachments/assets/61ddb4c5-60ad-4ed0-b014-085718945c8b" />

<img width="1917" height="1069" alt="image" src="https://github.com/user-attachments/assets/3e14da56-475d-46cb-ba9c-bca16b362225" />

<img width="1917" height="1069" alt="image" src="https://github.com/user-attachments/assets/649db072-9868-41b7-957b-f475beeeb5e7" />
<img width="1917" height="1069" alt="image" src="https://github.com/user-attachments/assets/48bd4217-5e04-4e53-955b-d5cad4328896" />

<img width="1917" height="1069" alt="image" src="https://github.com/user-attachments/assets/a33fb569-a992-4f02-8912-3d408a2c5754" />



# Final result

<img width="1917" height="1069" alt="image" src="https://github.com/user-attachments/assets/dcf0f34a-cfad-4cd1-9cde-7ef340771fed" />


<img width="1917" height="1069" alt="image" src="https://github.com/user-attachments/assets/8765e7ee-aa23-4821-b165-5fd243d190e9" />
