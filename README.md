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



