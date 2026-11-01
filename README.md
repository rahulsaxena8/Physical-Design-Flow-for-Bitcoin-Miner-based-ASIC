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
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Overview-1.png" width="800"/>
  <br>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Overview-2.png" width="800"/>
  <br>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Overview-3.png" width="800"/> 
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
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Hash.png" width="800"/>
</p>

## Piso
<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Piso.png" width="800"/>
</p>

## Sipo_Slice
<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Sipo_Slice.png" width="800"/>
</p>

Fusion compiler performs physically aware synthesis, incorporating preliminary floorplanning and placement which are further refined in subsequent physical design stages. The reports are generated using the following procedure: 

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/tcl_procedure.png" width="800"/>
</p>

## Hold Timing Report

<p align="center">
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/hold_timing_1.png" width="800"/>
 <br>
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/hold_timing_2.png" width="800"/>
</p>

Script for generating reports for 10 paths with maximum setup slack:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/10_max_paths.png" width="800"/>
</p>

## Setup Timing Report

<p align="center">
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/setup_timing_1.png" width="800"/>
 <br>
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/setup_timing_2.png" width="800"/>
</p>

Mapping the critical path on layout and schematic:
<!--
<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/timing_path_1.png" width="500"/>
</p>
-->
tcl command:

``` tcl
set tp1 [get_timing_paths -from bit_secure_2/lreset_sync/reset_sync_reg/CLK -to bit_secure_2/slice_9/sipo_bit/rd_addr_reg[1]/SETB]

change_selection [get_timing_paths $tp1]
```

 <p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Layout_1.png" width="300"/>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Schematic_1.png" width="500"/>
</p>

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Critical_path_1.png" width="800"/>
</p>

Identified the paths with this huge negative slack and declared them as false path:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/False_path.png" width="500"/>
</p>

The worst slack is reduced significantly.

<p align="center">
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/setup_timing_3.png" width="800"/>
 <br>
 <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/setup_timing_4.png" width="800"/>
</p>

Mapping the critical path on the layout and schematic:

 <p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Layout_2.png" width="300"/>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Schematic_2.png" width="500"/>
</p>

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Critical_path_2.png" width="800"/>
</p>

Extracted power report:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Synthesis/Power_report.png" width="800"/>
</p>

Total Dynamic power = 0.132 W.

Total Static power = Leakage Power = 0.110 W.

Total Power = 0.132 + 0.110 = 0.242 W.

# 3. Stage 2: STA

In this stage, we implement a simplified AT/RAT algorithm using tcl.

Following is an example timing graph:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/STA/timing_graph.png" width="800"/>
</p>

The weights on the edges between the nodes represent the gate delays. A set of 3 integers is assigned to every node, representing arrival time, required arrival time, and slack for each node.

Attached is the tcl script used for calculating these values: 

``` tcl
# Procedure to calculate the AT values for each node in a graph
# adjMat - adjacency matrix of the graph
# wMat - weight matrix of the graph
# nodes - list of nodes in the graph, each node represented as a list [id AT RAT SLACK]
proc calcAT {adjMat wMat nodes} {
    set loopCnt [llength $adjMat]
    for {set curNode 0} {$curNode < $loopCnt} {incr curNode} {
        for {set nextNode [expr $curNode+1]} {$nextNode < $loopCnt} {incr nextNode} {
            if {[lindex $adjMat $curNode $nextNode]} {
                set curAT [lindex $nodes $curNode 1]
                set weight [lindex $wMat $curNode $nextNode]
                set nextAT [lindex $nodes $nextNode 1]
                #puts "current: $curNode, next: $nextNode, weight: $weight, currentAT: $curAT"
                if {[expr $curAT + $weight] > [expr $nextAT]} {
                    lset nodes $nextNode 1 [expr {$curAT + $weight}]
                }
                
            }
        }
    }
    #puts "calcAT: $nodes\n"
    return $nodes
}

# Procedure to calculate the RAT values for each node in a graph
# adjMat - adjacency matrix of the graph
# wMat - weight matrix of the graph
# nodes - list of nodes in the graph, each node represented as a list [id AT RAT SLACK]
proc calcRAT {adjMat wMat nodes} {
    set loopCnt [expr [llength $adjMat] - 1]
    for {set curNode $loopCnt} {$curNode > 0} {incr curNode -1} {
        set curRAT [lindex $nodes $curNode 2]
        for {set prevNode [expr $curNode-1]} {$prevNode >= 0} {incr prevNode -1} {
            if {[lindex $adjMat $prevNode $curNode]} {
                set prevRAT [lindex $nodes $prevNode 2]
                set weight [lindex $wMat $prevNode $curNode]
                if {[expr {$curRAT - $weight}] < [expr $prevRAT]} {
                    lset nodes $prevNode 2 [expr {$curRAT - $weight}]
                }
            }
        }
    }
    #puts "calcRAT: $nodes"
    return $nodes
}

# Procedure to calculate the SLACK values for each node in a graph
# adjMat - adjacency matrix of the graph
# wMat - weight matrix of the graph
# nodes - list of nodes in the graph, each node represented as a list [id AT RAT SLACK]
proc calcSlack {adjMat wMat nodes T} {

    # Calculate the AT and RAT values for all nodes
    for {set i 0} {$i < [expr {[llength $nodes]}]} {incr i} {
        lset nodes $i 2 $T
    }
    set nodes [calcAT $adjMat $wMat $nodes]
    set nodes [calcRAT $adjMat $wMat $nodes]

    # Calculate the slack for each node and report if the slack is negative
    foreach node $nodes {
        set nodeid [lindex $node 0]
        set nodeRAT [lindex $node 2]
        set nodeAT [lindex $node 1]
        set slack [expr {$nodeRAT - $nodeAT}]
        lset nodes $nodeid 3 $slack
        if { $slack < 0} {
            puts "Node $node has negative slack: $slack"
        }
    }

    # Return the updated list of nodes with slack values
    return $nodes
}

set result [calcSlack $adjacencyMatrix $weightsMatrix $nodesList 12]
```
Sample inputs:

``` tcl
set adjacencyMatrix {      {0 1 1 1 0 0 0 0 0 0 0}
                           {0 0 0 0 1 1 0 0 0 0 0}
                           {0 0 0 0 0 1 0 0 0 0 0}
                           {0 0 0 0 0 1 1 0 0 0 0}
                           {0 0 0 0 0 0 0 1 0 0 0}
                           {0 0 0 0 0 0 0 1 1 1 0}
                           {0 0 0 0 0 0 0 0 1 1 0}
                           {0 0 0 0 0 0 0 0 0 0 1}
                           {0 0 0 0 0 0 0 0 0 0 1}
                           {0 0 0 0 0 0 0 0 0 0 1}
                           {0 0 0 0 0 0 0 0 0 0 0} }

set weightsMatrix 	{  {0 0 0 0 0 0 0 0 0 0 0}
                           {0 0 0 0 4 6 0 0 0 0 0}
                           {0 0 0 0 0 4 0 0 0 0 0}
                           {0 0 0 0 0 1 2 0 0 0 0}
                           {0 0 0 0 0 0 0 2 0 0 0}
                           {0 0 0 0 0 0 0 1 6 9 0}
                           {0 0 0 0 0 0 0 0 5 8 0}
                           {0 0 0 0 0 0 0 0 0 0 0}
                           {0 0 0 0 0 0 0 0 0 0 0}
                           {0 0 0 0 0 0 0 0 0 0 0}
                           {0 0 0 0 0 0 0 0 0 0 0} }

set nodesList 		{ {0 0 0 0} {1 0 0 0} {2 0 0 0} {3 0 0 0} {4 0 0 0} {5 0 0 0} 
                      {6 0 0 0} {7 0 0 0} {8 0 0 0} {9 0 0 0} {10 0 0 0} }

set result [calcSlack $adjacencyMatrix $weightsMatrix $nodesList 12]

```
The adjacency matrix describes the connections between the nodes, the weights matrix describes the propagation delay between the connected nodes. The RAT at the sinc node in this case is 12 units.

Corresponding outputs:

``` tcl
Node 0 has negative slack: -3
Node 1 has negative slack: -3
Node 2 has negative slack: -1
Node 5 has negative slack: -3
Node 9 has negative slack: -3
Node 10 has negative slack: -3

nodesList = { {0 0 -3 -3} {1 0 -3 -3} {2 0 -1 -1} {3 0 2 2} {4 4 10 6} {5 6 3 -3} 
                      {6 2 4 2} {7 7 12 5} {8 12 12 0} {9 15 12 -3} {10 15 12 -3} }
```

# 4. Stage 3: Floorplan

## Option 1: Automatic Floorplan

First option is automatic floorplanning. Following command sets the constraints of the floorplan:

``` tcl
set_auto_floorplan_constraints -core_utilization 0.5 -side_ratio {1 2}
```
=> Core utilization is 50 % and the aspect ratio is 1:2. 

The resultant floorplan and the congestion map is shown below:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/auto_floorplan.png" width="800"/>
</p>

Closeup of the layout:

 <p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/auto_floorplan_zoom1.png" width="400"/>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/auto_floorplan_zoom2.png" width="400"/>
</p>

## Option 2: Organizing the SRAMs into a matrix shape (fixed spaces)

The SRAMs are organized in the shape of a matrix with fixed spaces between them:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/matrix_floorplan.png" width="800"/>
</p>

The congestion improves significantly.

## Option 3: Organizing the SRAMs into a matrix shape (unfixed spaces)

Similar to the previous option, SRAMs are organized in matrix shape but the space between the cells is customizable through the following function:
```tcl 
create_macro_arrays
```

The layout was analyzed for various permutations of input arguments as shown in the screenshots:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/create_macro_array.png" width="800"/>
</p>

Layouts for some of the inputs are shown below:

 <p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/444444444.png" width="300"/>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/2 2 2 2 2 2 2 2 2.png" width="300"/>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/222222222.png" width="200"/>
  <br>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/8 4 2 10 20 32 4 50 50.png" width="400"/>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/333333333.png" width="400"/>
</p>

The input argument finally chosen to implement this option is ``` 3 3 3 20 20 20 20 20 20 ``` 

The corresponding layout with congestion map is as follows:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/custom_floorplan.png" width="800"/>
</p>

## Option 4: Divide and Conquer

This method is widely used in the designs having secondary hierarchy, such as the bit_coin. The bit_coin module is made up of 16 bit_top modules. Thus, only one bit_top module needs to be designed error free and then it can be copied and abutted to form most of the ASIC. This method saves a lot of labour and increases the accuracy substancially.

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Floorplan/hierarchial_floorplan.png" width="800"/>
 <br>
 Bitcoin Floorplan
</p>

# 5. Stage 4: Placement

Just as in the previous stages, the NDM lib was copied from the TA's directory, and the commands in the attached tcl script for placement was executed.

After completing the placement, another script containing commands for STA analysis was executed. 

The timing reports were extracted and two problamatic paths were identified.

## Path 1

### Timing Report

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Placement/timing_report_1.png" width="800"/>
</p>

### Layout and Schematic

 <p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Placement/layout_1.png" width="600"/>
  <br>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Placement/schematic_1.png" width="600"/>
</p>

## Path 2

### Timing Report

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Placement/timing_report_2.png" width="800"/>
</p>

### Layout and Schematic
 <p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Placement/layout_2.png" width="600"/>
  <br>
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Placement/schematic_2.png" width="600"/>
</p>

# 6. Stage 5: Clock Tree Synthesis

After running the tcl script for the stage, the implemented clock trees were exported using the gui as shown below:

 <p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/CTS/search.png" width="400"/>
  
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/CTS/apply.png" width="400"/>
</p>

The design contains two clocks named, lclk and hclk. The following snapshots display the clock distributions in the layout. The color scheme represents the levels of the clock tree.

## lclk

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/CTS/lclk_tree.png" width="800"/>
</p>

The lclk has 13 levels in total.

## hclk

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/CTS/hclk_tree.png" width="800"/>
</p>

The hclk has a total of 11 levels.

The global skew for hclk and lclk is shown in the following report: 

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/CTS/skew.png" width="800"/>
</p>

The levelized graphs for lclk and hclk:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/CTS/levelized_graph.png" width="800"/>
</p>

## lclk

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/CTS/lclk_graph.png" width="800"/>
</p>

## hclk 

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/CTS/hclk_graph.png" width="800"/>
</p>

# 7. Stage 6: Routing

A snapshot of the design at the end of last stage is imported and tcl script is executed.

The routing stage requires a lot of work when it comes to CPU calculations. Therefore, 4 cores were assigned to us for this stage. This is done by running the command:```set_host_options -max_cores 4``` 

Report after completion of the stage:

<p align="center">
  <img src="https://github.com/rahulsaxena8/Physical-Design-Flow-for-Bitcoin-Miner-based-ASIC/blob/Routing/Routing_report.png" width="800"/>
</p>

The routing iterations end with:

Number of shorts = 20.

Number of opens = 2.








 


