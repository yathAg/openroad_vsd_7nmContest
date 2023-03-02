# OpenRoad & VSD - 7nm PD Contest

This repository is designed to log my learnings and work during the contest.

The best guide can be found at the OpenRoad Flow Scripts tutorial [here](https://openroad-flow-scripts.readthedocs.io/en/latest/tutorials/FlowTutorial.html#introduction)

##  OpenRoad & Flow controllers

**OpenROAD** is an integrated chip physical design tool that takes a design from from RTL to GDSII, including synthesis, floorplanning, placement, routing, signoff parasitic extraction and timing analysis. 

It uses a hierarchical placement algorithm that aims to minimize wire length, and it provides several features to optimize timing and power consumption. OpenROAD is designed to be extensible and customizable, with a flexible architecture that allows users to add their own algorithms and features.


The OpenRoad project supports two main flow controllers.

1) - **ORFS (ORFlow)** is a flow controller that provides a collection of open-source tools for automated digital ASIC design from synthesis to layout. It provides a fully automated RTL-to-GDSII design flow, which includes Synthesis, Placement and Routing (PnR), STA (Static Timing Analysis), DRC (Design Rule Check) and LVS (Layout Versus Schematic) checks. ORFS aims to provide a flexible and customizable environment for digital ASIC design, allowing users to choose and combine different tools as needed. 

   - In ORFS, OpenROAD is used as a plugin for the physical design stage, and it can be configured and customized to meet the specific needs of the design project. The OpenROAD plugin in ORFS provides access to several advanced features, such as hierarchical placement, global routing, and detailed routing optimization.

   - ORFS  supports several public and private PDKs (under NDA) such as : GF180, Skywater130, ASAP7 etc.

2) **OpenLane** is a complete automated RTL-to-GDSII flow similiar to ORFS and is developed by Efabless for the skywater130 MPW Program 


More about the OpenRoad Project can be found [here](https://openroad.readthedocs.io/en/latest/main/README.html)

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

1) Install OpenRoad Flow Scripts

Clone repository
```
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
```
Install dependency using internal script ( It takes care of everything!!! )
```
cd OpenROAD-flow-scripts
cd tools/OpenROAD
sudo ./etc/DependencyInstaller.sh
```
Build & Install
```
cd ../..
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

2) Install Klayout.

 Installing from source but that takes a really ....really long time and the added fuss of getting the dependencies, making sure the path variable is correct and still after the process there were some issues.

Fortunately there is a .deb package and takes care of the installation with one click and can be found [here](https://www.klayout.de/build.html)

> Note: The Latest release (0.28.5) was installed



