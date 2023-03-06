# OpenROAD & VSD - 7nm PD Contest

This repository is designed to log my learnings and work during the contest.

The best guide can be found at the OpenROAD Flow Scripts tutorial [here](https://openroad-flow-scripts.readthedocs.io/en/latest/tutorials/FlowTutorial.html#introduction)

##  OpenROAD & Flow controllers

**OpenROAD** is an integrated chip physical design tool that takes a design from from RTL to GDSII, including synthesis, floorplanning, placement, routing, signoff parasitic extraction and timing analysis. 

It uses a hierarchical placement algorithm that aims to minimize wire length, and it provides several features to optimize timing and power consumption. OpenROAD is designed to be extensible and customizable, with a flexible architecture that allows users to add their own algorithms and features.


The OpenROAD project supports two main flow controllers.

1) - **OpenROAD-flow-scripts(ORFS)** is a flow controller that provides a collection of open-source tools for automated digital ASIC design from synthesis to layout. It provides a fully automated RTL-to-GDSII design flow, which includes Synthesis, Placement and Routing (PnR), STA (Static Timing Analysis), DRC (Design Rule Check) and LVS (Layout Versus Schematic) checks. ORFS aims to provide a flexible and customizable environment for digital ASIC design, allowing users to choose and combine different tools as needed. 

   - In ORFS, OpenROAD is used as a plugin for the physical design stage, and it can be configured and customized to meet the specific needs of the design project. The OpenROAD plugin in ORFS provides access to several advanced features, such as hierarchical placement, global routing, and detailed routing optimization.

   - ORFS  supports several public and private PDKs (under NDA). Available public PDK's are GF180, Skywater130, ASAP7 etc.

2) **OpenLane** is a complete automated RTL-to-GDSII flow similiar to ORFS and is developed by Efabless for the skywater130 MPW Program 


> More about the OpenROAD Project can be found [here](https://openroad.readthedocs.io/en/latest/main/README.html)

## Brief process of ORFS (or RTL to GDSII in general)

- Configuration: Once ORFS is installed, you can configure the framework to meet the specific needs of your design project. This involves specifying the design parameters, such as the target technology node, the design constraints, and the tool settings.

- Design entry: You can enter the design into ORFS in different ways, depending on your design entry method. ORFS supports different input formats such as Verilog

- Synthesis: The synthesis stage involves transforming the RTL design into a gate-level netlist. ORFS includes several open-source synthesis tools, such as Yosys and ABC, which can be used for this stage.

- Floorplanning: In the floorplanning stage, the placement of the different design modules within the chip area is determined. ORFS includes several floorplanning tools, such as RePlAce and Capo, which can be used for this stage.

- Placement: The placement stage involves determining the exact location of each gate or cell within the chip area. ORFS includes several placement tools, such as OpenROAD, which can be used for this stage.

- Routing: The routing stage involves connecting the gates and cells using metal wires to form a complete circuit. ORFS includes several routing tools, such as FastRoute and TritonRoute, which can be used for this stage.

- Layout verification: After the routing stage, the design is verified for layout correctness using tools such as Magic, which is included in ORFS.

- GDSII generation: Once the design is verified, the final GDSII layout file is generated using ORFS tools such as Magic and KLayout.

## Installing and setting up ORFS

The best resource for setting up the toolchain can be found [here](https://openroad-flow-scripts.readthedocs.io/en/latest/user/BuildLocally.html). A shorter version of the steps  is documented below.

Clone repository
```
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
```
Install dependency using internal script ( It takes care of all the additional packages)
```
cd OpenROAD-flow-scripts
sudo ./etc/DependencyInstaller.sh
```
Build & Install ( This  installs all the required scripts and the OpenRoad tool as well)
```
./build_openroad.sh --local
```

Verify Installation
```
source ./setup_env.sh
yosys -help
openroad -help
exit
```

> Make sure that to remember the location of where the ORFS repository is cloned and every time a new terminal is launched, the `setup_env.sh` file is sourced.


## Updating ORFS

ORFS is constantly evolving and it is a good idea to update the files from time to time. To update the ORFS files execute the commands below 

```
cd OpenROAD-flow-scripts
git checkout master
git pull
git submodule update
```


## The really simple way for RTL to GDSII!!!

To understand the beauty of the OpenROAD Flow scripts, below are the really simple steps to generate the GDSII files for the `ibex` RISC V processor and the `ASAP7` PDK.

> More about the ibex RISC V processor can be found [here](https://github.com/lowRISC/ibex)

Change your current directory to the flow directory.
```
cd flow
```

Uncomment the ` DESIGN_CONFIG=./designs/asap7/ibex/config.mk` line in the makefile

```
# DESIGN_CONFIG=./designs/asap7/gcd/config.mk
 DESIGN_CONFIG=./designs/asap7/ibex/config.mk
# DESIGN_CONFIG=./designs/asap7/aes/config.mk
```
>Note: this is only for the ibex processor using asap7, other designs will have their other respective design files

run make

```
make
```

And thats it!!! with a single command OpenROAD takes care of converting the RTL to GDSII. It took about 15 minutes on my machine to have the final gds and the process completes with the message.
 
 ```
[INFO] Writing out GDS/OAS 'results/asap7/ibex/base/6_1_merged.gds'
Elapsed time: 0:01.96[h:]min:sec. CPU time: user 1.82 sys 0.14 (100%). Peak memory: 475528KB.
cp results/asap7/ibex/base/6_1_merged.gds results/asap7/ibex/base/6_final.gds

 ```


 OpenROAD offers an interactive to analyse the generated GDSII and the gui is launched using

 ```
 make gui_final
 ```

![GUI](resources/img1.png)


## Understanding the directory structure

![directory](resources/img2.jpg)


## Understanding and viewing Reports

The three major metrics to understand the performance and functionality of the design are
- Timing ( Target -> worst negative slack = O)
- Power  ( Lower the better)
- Design Area ( Lower the better )

### Reporting and understanding the timing results

Use the following commands in the Tcl Commands section of GUI:

```
report_worst_slack
report_tns
report_wns
```

An example of the reported Timing results is
```
worst slack -107.32
tns -870.89
wns -107.32
```
> Note the timing constraints in this situation are not met.


- Negative Slack (NS): Negative slack is the amount of time by which a signal arrives later than it is required to arrive at a particular point in the circuit. It is calculated as the difference between the required arrival time and the actual arrival time of a signal. A negative slack value indicates that the circuit is not meeting its timing requirements and may result in timing violations.

- Worst Negative Slack (WNS): Worst negative slack (WNS) is the most negative value of the slack across all paths in the circuit. It represents the worst-case timing violation in the circuit.

- Total Negative Slack (TNS): Total negative slack (TNS) is the sum of all the negative slack values across all paths in the circuit. It represents the overall timing violation in the circuit.


### Reporting and understanding power usage 

The power is reported by 
```
report_power
```

An example of the reported power is 

```
Group                  Internal  Switching    Leakage      Total
                          Power      Power      Power      Power (Watts)
----------------------------------------------------------------
Sequential             1.36e-03   2.68e-04   2.39e-07   1.63e-03  13.4%
Combinational          4.35e-03   6.11e-03   1.99e-06   1.05e-02  86.6%
Macro                  0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
Pad                    0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
----------------------------------------------------------------
Total                  5.70e-03   6.38e-03   2.23e-06   1.21e-02 100.0%
                          47.2%      52.8%       0.0%

```

- Sequential Power Usage: Sequential power usage refers to the power consumed by the flip-flops and latches in a circuit. These elements store the state of the circuit, so they consume power even when the circuit is not actively switching. 

- Combinational Power Usage: Combinational power usage refers to the power consumed by the logic gates and interconnects in a circuit. These elements do not store any state, so they only consume power when the circuit is actively switching. 

- Macro Power Usage: Macro power usage refers to the power consumed by large IP blocks or subcircuits in the design. These blocks are typically provided by third-party vendors and can have a significant impact on the overall power consumption of the chip.

- Pad Power Usage: Pad power usage refers to the power consumed by the input/output (I/O) pads of the chip. These pads interface the chip with the outside world and are typically designed to meet specific electrical standards. 

### Reporting Area Utilization

View design area and its core utilization:

```
report_design_area
```

An example of the reported area is 

```
Design area 2489 u^2 45% utilization.
```

## Optimizing the design for 0 wns and drc free