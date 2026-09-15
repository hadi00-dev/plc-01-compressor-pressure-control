**# Digital Control Logic**

**# Compressor Receiver Pressure Control System**

## 1.0 Project Overview

This repository contains the software assets and Functional Design Specification (FDS) for the automated pressure regulation of a compressor receiver vessel. The objective is to maintain nominal operating pressure within predefined setpoints using discrete logic execution.

**Target Hardware/Environment:** RSLogix 500 Micro Starter Lite - free license

**Programming Language:** Ladder Diagram (LD)


## 2.0 System Architecture & Process Flow

The physical architecture consists of a single pressure-generating pump and a receiver tank monitored by two discrete pressure switches (Low and High). 

*Please refer to the process diagram:* `Process_Diagram_Compressor.png`


## 3.0 Control Philosophy

The control system operates under the following automated sequence:

1. **Pressure Generation:** The primary pump is interlocked to operate continuously until the high-pressure threshold is achieved.
2. **Cut-In / Cut-Out Thresholds:** 
    * The low-pressure switch actuates (closes) at pressures ≥ 90 PSI.
    * The high-pressure switch actuates (closes) at pressures ≥ 110 PSI.
3. **Status Indication:** A local Run Indicator (RUN IND) illuminates continuously whenever the vessel pressure meets or exceeds the 90 PSI threshold.
4. **System Hysteresis:** Upon reaching 110 PSI, the pump de-energizes. It will not re-energize until the system pressure drops below 90 PSI (both switches open), establishing a 20 PSI control deadband.


## 4.0 I/O Allocation Schedule

| Tag / Address | I/O Type | Device Description | Field State Definition |
| :--- | :--- | :--- | :--- |
| `I:0/0` | Digital Input  | PSL-01: Low Pressure Switch | Closed at ≥ 90 PSI |
| `I:0/1` | Digital Input  | PSH-02: High Pressure Switch | Closed at ≥ 110 PSI |
| `O:0/0` | Digital Output | P-01: Main Pressure Pump | 1 = Energized / Running |
| `O:0/1` | Digital Output | IL-01: Status Indicator Light | 1 = Illuminated |


## 5.0 Simulation & Verification Protocol (FAT Criteria)

The logic was validated via RSLogix Emulate software. The system successfully passed the following Factory Acceptance Test (FAT) simulation states:

* **State 1 (Startup):** Initial power-up. `I:0/0` (0), `I:0/1` (0). **Result:** Pump `O:0/0` ENERGIZES. Indicator `O:0/1` remains OFF.
* **State 2 (Charging):** Pressure reaches 90 PSI. `I:0/0` (1), `I:0/1` (0). **Result:** Pump `O:0/0` REMAINS ENERGIZED. Indicator `O:0/1` ENERGIZES.
* **State 3 (Setpoint Achieved):** Pressure reaches 110 PSI. `I:0/0` (1), `I:0/1` (1). **Result:** Pump `O:0/0` DE-ENERGIZES. Indicator `O:0/1` REMAINS ENERGIZED.
* **State 4 (Discharging):** Pressure falls below 110 PSI. `I:0/0` (1), `I:0/1` (0). **Result:** Pump `O:0/0` REMAINS DE-ENERGIZED. Indicator `O:0/1` REMAINS ENERGIZED.
* **State 5 (Re-charging):** Pressure falls below 90 PSI. `I:0/0` (0), `I:0/1` (0). **Result:** Pump `O:0/0` RE-ENERGIZES. Indicator `O:0/1` DE-ENERGIZES.


## 6.0 Software Assets

* The raw ladder logic project file (`.RSS`) can be found in the `/src/` directory.
* A complete PDF export of the ladder logic program is available in the `/docs/` directory.