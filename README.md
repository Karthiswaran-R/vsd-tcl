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




Here, the TCL script becomes a *flow controller*, capable of:

* Launching synthesis and analysis tools.
* Managing data dependencies between stages.
* Evaluating timing performance quantitatively.

**OpenTimer** performs the timing graph analysis using liberty, Verilog, and SDC inputs, applying standard STA principles:

```
Slack = Required_Time – Arrival_Time
```

A negative slack indicates a timing violation.




### Traking no of * & //// in openMSP430.synth.v





# Final result



