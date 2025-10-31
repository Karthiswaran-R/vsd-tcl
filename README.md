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


### Scenario 2:
user provides the name of .csv file but it doesnot exist 
<img width="1173" height="610" alt="image" src="https://github.com/user-attachments/assets/f7acd089-40f0-46ed-bfb4-b1f8a50f3afc" />

### Scenario 3:
user requests for help regarding the excel sheet content and execution using --help 
<img width="1271" height="643" alt="image" src="https://github.com/user-attachments/assets/e9d8d06e-2cd6-48b6-b3a1-9abc7fc4d554" />

## Creating CSV File
<img width="1271" height="643" alt="image" src="https://github.com/user-attachments/assets/443ae576-1fa9-450f-bb47-4da1b0bfe43a" />


Source the UNIX shell to tcl script by passing the csv file
```
tclsh my_synth.tcl $argv[1]

```
<img width="1343" height="677" alt="image" src="https://github.com/user-attachments/assets/4e49fea0-b561-4cc8-96c1-99abe72130dc" />

 height="1069" alt="image" src="https://github.com/user-attachments/assets/8765e7ee-aa23-4821-b165-5fd243d190e9" />
