# Physical Design Flow for ASIC based on Bitcoin-Miner

1) Implemented physical design stages for an ASIC having architecture derived from Bitcoin mining chip on Synopsis Fusion Compiler using SAED 32 nm technology.
2) Implemented each stage by importing NDM libraries for the previous stage and running tcl scripts.
3) Generated block diagrams and schematics after the design synthesis. Analyzed the timing reports and checked
for slack violations. Identified the critical paths and set up the false paths. Obtained static power = 110 mW and
dynamic power = 242 mW from the power report.
4) Conducted automatic and custom floorplanning. Did congestion analysis for each floorplan by observing the
total number of overflows in all the metal layers.
5) Generated post-placement timing reports and analyzed two paths with setup violations. Found negative slacks
of -0.05 ns and -0.23 ns for the paths and proposed solutions to solve the violations.
6) Generated clock tree distributions over the layout. lclk network is made up of 11 levels and has global skew of
0.33 ns, while the hclk has 13 levels and a global skew of 0.25 ns. Both the clocks operate at a frequency of 1 GHz.
7) After the routing stage, number of shorts = 20 and number of opens = 2.

I contributed by implementing Sythesis and Floorplan stage.

# 1. Overview

 <p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Overview-1.png" width="800"/>
  <br>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Overview-2.png" width="800"/>
  <br>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Overview-3.png" width="800"/> 
  <br>
  Taken from Chapter 2 of <i>Bitcoin: A Low Power case study by Synopsis, 2017.</i> 
</p>
In this project, we will implement a simpler version of the Bitcoin case study designed by Synopsis while focusing on the backend side (physical design) and taking the frontend side (RTL implementation and verification) as given.

# 2. Stage 1: Synthesis
TCL file <a href="bitcoin_stage_1.tcl">bitcoin_stage_1.v</a> was copied to the script editor and the commands were run line by line.
The Verilog file is imported and the rtl is synthesized into functional blocks and standard cells.
Screenshots of some of the synthesized blocks are as follows:

## Hash
<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Hash.png" width="800"/>
</p>

## Piso
<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Piso.png" width="800"/>
</p>

## Sipo_Slice
<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Sipo_Slice.png" width="800"/>
</p>

Fusion compiler performs physically aware synthesis, incorporating preliminary floorplanning and placement which are further refined in subsequent physical design stages. The reports are generated using the following procedure: 

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/tcl_procedure.png" width="800"/>
</p>

## Hold Timing Report

<p align="center">
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/hold_timing_1.png" width="800"/>
 <br>
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/hold_timing_2.png" width="800"/>
</p>

Script for generating reports for 10 paths with maximum setup slack:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/10_max_paths.png" width="800"/>
</p>

## Setup Timing Report

<p align="center">
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/setup_timing_1.png" width="800"/>
 <br>
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/setup_timing_2.png" width="800"/>
</p>

Mapping the critical path on layout and schematic:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/timing_path_1.png" width="500"/>
</p>

 <p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Layout_1.png" width="300"/>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Schematic_1.png" width="500"/>
</p>

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Critical_path_1.png" width="800"/>
</p>

Identified the paths with this huge negative slack and declared them as false path:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/False_path.png" width="500"/>
</p>

The worst slack is reduced significantly.

<p align="center">
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/setup_timing_3.png" width="800"/>
 <br>
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/setup_timing_4.png" width="800"/>
</p>

Mapping the critical path on the layout and schematic:

 <p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Layout_2.png" width="300"/>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Schematic_2.png" width="500"/>
</p>

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Images/Critical_path_2.png" width="800"/>
</p>
 


