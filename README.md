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

## **Day 2: Automated Design Setup and Synthesis Flow Initialization**

This stage focuses on **input preparation and validation** before synthesis.
<img width="1742" height="888" alt="image" src="https://github.com/user-attachments/assets/d889dd4f-65b8-4441-8704-4f4881bebb06" />

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

<img width="1735" height="877" alt="Screenshot 2025-11-05 145542" src="https://github.com/user-attachments/assets/9831709f-a436-4436-8012-5198e3188f77" />

<img width="1733" height="885" alt="image" src="https://github.com/user-attachments/assets/15ca2f8f-5873-4777-a73e-b303593dd578" />

<img width="1751" height="892" alt="image" src="https://github.com/user-attachments/assets/efa1046a-0aa5-4878-a3ca-bc3314ea541d" />

---
## **Day 3: Mapping CSV Constraints to SDC for Yosys**

### Objective: 
Automate the generation of Synopsys Design Constraints (SDC) from openMSP430_design_constraints.csv for use in synthesis.

### Key Steps:

* CSV Parsing

* Read the CSV file containing timing constraints for clocks, inputs, and outputs.

* Categorize rows based on type: CLOCKS, INPUTS, OUTPUTS.

* Identify single-bit signals vs. multi-bit buses using regular expressions.

Clock Definition

Extract clock period, duty cycle, and latency from CSV.

<img width="1707" height="872" alt="image" src="https://github.com/user-attachments/assets/a50fded3-8d07-4281-8953-fc7bfca846dd" />

### Generate SDC clock commands:
```bash
create_clock -period <T> -waveform {0 <T/2>} [get_ports <clk_port>]
set_clock_latency -source <value> [get_clocks <clk_name>]
```

### Input / Output Constraints

Generate SDC commands for input delays and output delays:
```bash
set_input_delay -clock <clk_name> <delay_value> [get_ports <signal_name>]
set_output_delay -clock <clk_name> <delay_value> [get_ports <signal_name>]
```

Handle bus signals ([0:7]) with wildcard expansion: [get_ports my_bus[*]].


### Automation Highlights

Store clocks and signals in structured arrays or dictionaries.

Loop over all CSV entries and generate corresponding SDC lines automatically.

Validate existence of each signal in the RTL netlist before SDC generation.

<img width="1124" height="431" alt="Screenshot 2025-11-05 160211" src="https://github.com/user-attachments/assets/8ffcf635-9df4-4369-8aaa-5c8762bfbabc" />

---

## **Day 4: Feeding RTL Netlist & Standard Cell Library to Yosys**

### Objective: 
Synthesize the openMSP430 RTL using Yosys while respecting timing constraints.

### Key Steps:

* Prepare RTL and Library

* Read RTL Verilog file: openMSP430.sv.

* Read standard cell library (cells.lib) to map gates to timing and area.

* Integrate Timing Constraints

* Include the SDC generated in Day 3:

* read_sdc openMSP430.sdc


Synthesis Script (synth.ys)
```bash
read_verilog openMSP430.sv
read_liberty -lib cells.lib
synth -top top_module
write_verilog openMSP430.synth.v
```
### Synthesised RTL

 <img width="1740" height="889" alt="image" src="https://github.com/user-attachments/assets/feebc2ad-0d56-4d39-ba47-0d688dce3492" />

Generates a gate-level netlist that honors all SDC constraints.

Hierarchy & Error Checks

Ensure all modules are properly instantiated.

Perform hierarchy check:

check_hierarchy

Detect missing or unrecognized signals, generating clear error messages.

Output Constraints

Add set_output_delay and set_load commands automatically based on CSV or design specifications.

<img width="882" height="737" alt="image" src="https://github.com/user-attachments/assets/1ed61a11-f7eb-4e26-8ca2-805c2b20c752" />

## **Day 5: Timing Analysis with OpenTimer**

### Objective: 
Perform automated static timing analysis (STA) using OpenTimer and generate a Quality of Results (QoR) report.

### Key Steps:

* Prepare OpenTimer Configuration

* Use generated gate-level netlist (openMSP430.synth.v), SDC (openMSP430.sdc), and standard cell library (cells.lib).

* Generate OpenTimer .conf file to define analysis parameters.

### Run STA

opentimer -lib cells.lib -verilog openMSP430.synth.v -sdc openMSP430.sdc -report timer_report.txt

<img width="1717" height="889" alt="image" src="https://github.com/user-attachments/assets/5a713498-4fe5-49a9-8fbe-148287361fcb" />

### Parse QoR Metrics

Extract critical metrics:

* WNS (Worst Negative Slack)

* TNS (Total Negative Slack)

* Setup / Hold Violations

* Instance Count

Runtime

Automate extraction using TCL or Python scripts for logs and readable tables.

### Slack Analysis
```bash
Slack = Required_Time − Arrival_Time
```
Negative slack indicates timing violations that must be corrected.

### Flow Automation

Master TCL script orchestrates:
```bash
CSV → SDC → Yosys → OpenTimer → QoR metrics
```
Handles errors, dependencies, and logs all outputs.

Here, the TCL script becomes a *flow controller*, capable of:

* Launching synthesis and analysis tools.
* Managing data dependencies between stages.
* Evaluating timing performance quantitatively.

<img width="1738" height="883" alt="Screenshot 2025-11-05 211843" src="https://github.com/user-attachments/assets/bc4d4835-0474-4bda-ae7e-271803e500b3" />

**OpenTimer** performs the timing graph analysis using liberty, Verilog, and SDC inputs, applying standard STA principles:

```bash
Slack = Required_Time – Arrival_Time
```

A negative slack indicates a timing violation.

<img width="1738" height="883" alt="Screenshot 2025-11-05 211843" src="https://github.com/user-attachments/assets/cc8db66d-5287-4372-a062-c938b035e852" />

## Final result

<img width="1740" height="889" alt="Screenshot 2025-11-05 214910" src="https://github.com/user-attachments/assets/bf30ca31-6f12-4366-878b-352ff1f24958" />
<img width="882" height="737" alt="Screenshot 2025-11-05 215214" src="https://github.com/user-attachments/assets/b210d02e-d512-4f94-8ac4-b1152ee2a8d5" />
<img width="1717" height="889" alt="Screenshot 2025-11-05 215509" src="https://github.com/user-attachments/assets/89ea2b43-053e-4c3d-9ad5-2a78f324bec5" />
<img width="1744" height="394" alt="Screenshot 2025-11-05 215827" src="https://github.com/user-attachments/assets/21460840-b48e-44ad-b007-e486d3175c3e" />
<img width="1730" height="896" alt="Screenshot 2025-11-05 215900" src="https://github.com/user-attachments/assets/9e65e333-be6c-468c-8d14-e4ddc889b627" />

---
## Conclusion

This project demonstrates how TCL scripting bridges design data and EDA automation, forming a scalable and reusable infrastructure for open-source VLSI flows.

**“Automation is the foundation of innovation in silicon design.”**

---

##  Author

**Karthiswaran R**  
_B.E Electronics Engineering (VLSIDT)_  
**VLSI Design Hub**  

**GitHub:** [https://github.com/Karthiswaran-R](https://github.com/Karthiswaran-R)  
**Website:** [https://vlsidesginhub.netlify.app/linux](https://vlsidesginhub.netlify.app/linux)
