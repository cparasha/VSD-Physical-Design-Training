# VSD-Physical-Design-Training
## Day 1 - Inception of Open Source EDA
### Skywater PDK Files
The Skywater PDK files we are working with are described under $PDK_ROOT. There are three subdirectories needed for the workshop:
![image](https://user-images.githubusercontent.com/127513356/224341244-75045762-a4da-4310-a939-c0444dd26ba2.png)
1. Skywater-pdk – Contains all the foundry provided PDK related files<br>
2. Open_pdks – Contains scripts that are used to bridge the gap between closed-source and open-source PDK to EDA tool compatibility<br>
3. Sky130A – The open-source compatible PDK files<br>

### Invoking OpenLane
![image](https://user-images.githubusercontent.com/127513356/224342415-6223f226-41fb-45d2-a114-e96918a28a6a.png)
1. ./flow.tcl is the script which runs the OpenLANE flow
2. OpenLANE can be run interactively or in autonomous mode
3. To run interactively, use the -interactive option with the ./flow.tcl script

### Package Importing
Different software dependencies are needed to run OpenLANE. To import these into the OpenLANE tool we need to run:
![image](https://user-images.githubusercontent.com/127513356/224342668-880450cf-2c72-441a-bee3-ec7d436d3e31.png)

### Design Folder
All designs run within OpenLANE are extracted from the openlane/designs folder:
![image](https://user-images.githubusercontent.com/127513356/224342986-371ad1e7-74aa-41d2-9dd3-93fac7952ec6.png)

### Prepare Design
![image](https://user-images.githubusercontent.com/127513356/224344075-3d016fe4-9f9b-4e30-8a0b-2d431f8d6130.png)

### Synthesis
![image](https://user-images.githubusercontent.com/127513356/224344282-88942a32-320d-40b8-b45a-1396b86caf04.png)

This command runs Yosys simulator and ABC


## Day 2 Chip Floorplanning and Standard Cells
In Floorplanning we typically set the:
1. Die Area
2. Core Area
3. Core Utilization
4. Aspect Ratio
5. Place Macros
6. Power distribution network (Normally done here but done later in OpenLANE)
7. Place input and output pins

### Aspect Ratio and Utilization Factor
Two key descriptions of a floorplan are utilization and aspect ratio. The amount of area of the die core, the standard cells are taking up is called utilization. Normally we go for 50-70% utilization, or utilization factor of 0.5-0.7. Keeping within this range allows for optimization of placement and realizable routing of a system. Aspect ratio can specify the shape of your chip defining the height of the core area divided by the width of the core area. An aspect ratio of 1 discribes the chip as a square.

### Preplaced Cells
Preplaced cells, or MACROs, are important to enable hierarchical PnR flow. Preplaced cells enable VLSI engineers to granularize a larger design. In floorplanning we define locations and blockages for preplaced cells. Blockages are needed to ensure no standard cells are placed where the placeplaced cells are located.

### Decoupling Capacitors
Decoupling capacitors are placed local to preplaced cells during Floorplanning. Voltage drops associated with interconnect wires can heavily affect our noise margin or put it into an indeterminate state. Decoupling capacitor is a big capacitor located next to the macros to fix this problem. The capacitor will charge up to the power supply voltage over time and it will work as a charge reservoir when a transition is needed by the circuit instead of the charge coming from the power supply. Therefore it “decouples” the circuit from the main supply. The capacitor acts like the power supply.

### Power Planning
Power planning during the Floorplanning phase is essential to lower noise in digital circuits attributed to voltage droop and ground bounce. Coupling capacitance is formed between interconnect wires and the substrate which needs to be charged or discharged to represent either logic 1 or logic 0. When a transition occurs on a net, charge associated with coupling capacitors may be dumped to ground. If there are not enough ground taps charge will accumulate at the tap and the ground line will act like a large resistor, raising the ground voltage and lowering our noise margin. To bypass this problem a robust PDN with many power strap taps are needed to lower the resistance associated with the PDN.

### Pin Placement
Pin placement is an essential part of floorplanning to minimize buffering and improve power consumption and timing delays. The goal of pin placement is to use the connectivity information of the HDL netlist to determine where along the I/O ring a specific pin should be placed. In many cases, optimal pin placement will be accompanied with less buffering and therefore less power consumption. After pin placement is formed we need to place logical cell blockages along the I/O ring to discriminate between the core area and I/O area.

### Floorplanning with OpenLANE
To run floorplan in OpenLANE:

![image](https://user-images.githubusercontent.com/127513356/224345654-8d346625-56b2-497e-8e0c-d309c2a36f09.png)

The output of the floorplanning phase is a DEF file which describes core area and placement of standard cells:
![image](https://user-images.githubusercontent.com/127513356/224347744-0d7d25b5-42e8-4ea9-b0d9-4621a18004e4.png)


### Viewing Floorplan in Magic
To view our floorplan in Magic we need to provide three files as input:

1. Magic technology file (sky130A.tech)
2. Def file of floorplan
3. Merged LEF file

![image](https://user-images.githubusercontent.com/127513356/224349375-ca6230c3-268e-4fd3-82bf-98db10206d0b.png)

![image](https://user-images.githubusercontent.com/127513356/224349800-ebd5d64e-2542-478a-98bf-7aa56d1f01a3.png)

### Placement
The next step in the Digital ASIC design flow after floorplanning is placement. The synthesized netlist has been mapped to standard cells and floorplanning phase has determined the standard cells rows, enabling placement. OpenLANE does placement in two stages:

1. Global Placement - Optimized but not legal placement. Optimization works to reduce wirelength by reducing half parameter wirelength
2. Detailed Placement - Legalizes placement of cells into standard cell rows while adhering to global placement

To do placement in OpenLANE:

![image](https://user-images.githubusercontent.com/127513356/224349984-26aee50c-36a8-4283-8cbd-d3ab9a029e5b.png)



### Viewing Placement in Magic
We use the same command as before to view the placement in Magic

![image](https://user-images.githubusercontent.com/127513356/224395570-ef8fff18-510b-4829-9106-dd6eb1296ea9.png)

![image](https://user-images.githubusercontent.com/127513356/224395870-c80c95a5-ca7e-4aa6-aaa3-376da27a3367.png)

### Standard Cell Design Flow
Cell design is done in 3 parts:

1. Inputs - PDKs (Process design kits), DRC & LVS rules, SPICE models, library & user-defined specs.
2. Design Steps - Design steps of cell design involves Circuit Design, Layout Design, Characterization. The software GUNA used for characterization. The characterization can be classified as Timing characterization, Power characterization and Noise characterization.
3. Outputs - Outputs of the Design are CDL (Circuit Description Language), GDSII, LEF, extracted Spice netlist (.cir), timing, noise, power.libs, function.


### Standard Cell Characterization
Standard Cell Libraries consist of cells with different functionality/drive strength. These cells need to be characterized in liberty files to be used by synthesis tools to determine optimal circuit arrangement. The open-source software GUNA is used for characterization.

Characterization is a well-defined flow consisting of the following steps:

1. Link Model File of CMOS containing property definitions
2. Specify process corner(s) for the cell to be characterized
3. Specify cell delay and slew thresholds percentages
4. Specify timing and power tables
5. Read the parasitic extracted netlist
6. Apply input or stimulus
7. Provide necessary simulation commands

## Day 3 Design Library Cell

### Spice Simulations
To simulate standard cells spice deck wrappers will need to be created around our model files.
SPICE deck will comprise of:

1. Component connectivity, including substrate taps
2. Output load capacitance
3. Component values
4. Node names
5. Simulation commands
6. Model include statements

To plot the output waveform of the spice deck we will use ngspice. The steps to run the simulation on ngpice are as follows:

1. Source the .cir spice deck file
2. Run the spice file by: run
3. Run: setplot → allows you to view any plots possible from the simulations specified in the spice deck
4. Select the simulation desired by entering the simulation name in the terminal
5. Run: display to see nodes available for plotting
6. Run: plot  vs  to obtain output waveform


### Switching Threshold of a CMOS Inverter
CMOS cells have three modes of operation:

1. Cutoff - No inversion
2. Triode - Inversion but no pinchoff in channel
3. Saturation - Inversion and pinchoff in channel

The voltages at which the switch between the modes of operation happens is dependent on the threshold voltage of the device(s). Threshold voltage is a function of the W/L ratio of a device, therefore varying the W/L ratio will vary the output waveform of CMOS devices.

To enable efficient description of the varying waveforms a single parameter called switching threshold is used. Switching threshold is defined at the intersection of Vin = Vout. A perfectly symmetrical device will have a switching threshold such that Vin = Vout = VDD/2.

### 16 Mask CMOS Process Steps

1. Substrate Selection : Selection of base layer on which other regions will be formed.
2. Create an active region for transistors : SiO2 and Si3N2 deposited. Pockets created using photoresist and lithography.
3. Nwell & Pwell formation : Pwell uses boron and nwell uses phosphorus. Drive in diffusion by placing it in a high temperature furnace.
4. Creating Gate terminal : For desired threshold value NA (doping Concentration) and Cox to be set.
5. Lightly Doped Drain (LDD) formation : LDD done to avoid hot electron effect and short channel effect.
6. Source and Drain formation : Forming the source and drain.
7. Contacts & local interconnect Creation : SiO2 removed using HF etch. Titanium deposited using sputtering.
8. Higher Level metal layer formation : Upper layers of metals deposited.


### Magic Layout View of Inverter Standard Cell


![image](https://user-images.githubusercontent.com/127513356/224404497-120bc3dd-2c43-4b6a-ab25-dd8311585afa.png)

![image](https://user-images.githubusercontent.com/127513356/224405933-d6711e4a-7c64-40a7-981e-a2c909d92da9.png)

![image](https://user-images.githubusercontent.com/127513356/224406093-9847439f-e0eb-47c5-8c30-0b430cb9e6de.png)

### Magic Key Features:

1. Color Palette - Defines layers and associated colors continuous DRC
2. Device Inference - Automatic recognition of NMOS and PMOS devices

### Device Inference
We can select the specific layer/device by hovering over the object and pressing, s, until we traverse the hierarchy to the desired object.
![image](https://user-images.githubusercontent.com/127513356/224407026-27fe7458-c480-46b8-9608-d8287dcdd605.png)

### Parasitics Extraction with Magic
To extract the parasitic spice file for the associated layout one needs to create an extraction file:

![image](https://user-images.githubusercontent.com/127513356/224407541-0fb02853-3f99-4f90-bd6e-e3d4550b55fe.png)

After generating the extracted file we need to output the .ext file to a spice file:

![image](https://user-images.githubusercontent.com/127513356/224408025-144c5a95-e401-40a8-a0a1-da012ca62d92.png)

![image](https://user-images.githubusercontent.com/127513356/224408168-ddf27736-aaea-48c7-b7c7-5f37a7618668.png)

![image](https://user-images.githubusercontent.com/127513356/224408527-6bfc780f-c144-4cd0-a12b-55f056ab79d8.png)

### Spice Wrapper for simulation
![image](https://user-images.githubusercontent.com/127513356/224424204-cdc52bfc-64d9-4df8-b62d-8424768992f9.png)

To run the simulation with ngspice, invoke the ngspice tool with the spice file as input:
![image](https://user-images.githubusercontent.com/127513356/224423817-80862297-67f1-4743-80cf-748bd6b2d67e.png)

The plot can be viewed by plotting the output vs time while sweeping the input:
![image](https://user-images.githubusercontent.com/127513356/224424104-951de615-46fd-4163-b395-39d31f163f3c.png)


## Day 4 - Layout Timing Analysis and CTS

#### STA Fundamentals
All real cells have finite propagation delays. This can be found from the dotLIB file of a cell, which contains propagation delay value for several combinations of input_slew and output_load.

Static Timing Analysis(STA) is a method used in digital circuit design to ensure that the circuit meets its timing requirements. Timing requirements are critical in digital circuit design because if the signals do not arrive at the right time, errors can occur, and the circuit may not function as intended.

Timing requirements can mainly be required in two categories:

1. Setup requirement: Setup requirement refers to a timing requirement that ensures that the input signal to a flip-flop is stable for a specified amount of time before the clock edge that triggers that flip-flop.
2. Hold requirement: Hold requirement refers to a timing requirement that ensures that the output signal to a flip-flop remains stable for a specified amount of time after the clock edge that triggers that flip-flop.


#### Clock Tree Synthesis (CTS):

Clock Tree Synthesis (CTS) is the process of designing clock distribution network from the clock port to sequential pins. The clock distribution network contains mainly of clock buffers and clock gates.

CTS becomes mandatory because a single clock port cannot drive all sequential pins inside a design. Further CTS should achieve following goals:

1. Clock should reach all sequential pins at the same time i.e., there should be minimal skew.
2. Variation in clock period, called jitter, should be minimal
 
These requirements are better met if clock tree has following properties:

1. Each layer should have same buffer cells
2. Each cell should drive the same load

#### H-Tree algorithm
In order to minimize skew, clock nets are not laid in an unorderly fashion. Rather, it is ensured that the parent branch of the clock net intersects the child branch at its midpoint. When this is ensured for all branches:

Each sequential pin is equidistant from the clock port and all sequential pins see the same clock delay.
Resultant routing structure looks like the letter "H", hence giving the algorithm its name

Repeaters are added to long branches to ensure signal integrity. It is attempted that equal number of repeaters lie between the clock port and each sequential clock pin.

#### Crosstalk and clock net shielding
Crosstalk is the phenomena wherein state changes on an aggressor net can induce glitches or delays in a nearby victim net. This happens because capacitances of nearby nets are coupled with each other.

Clock Tree Shielding: Clock nets are extremely sensitive to glitches. A single glitch on clock net can corrupt functioning of the circuit. In order to avoid glitches on clock nets due to crosstalk, they are surrounded by power or ground nets which do not toggle. This process of protecting clock tree from crosstalk is called Clock net shielding.


### Labs - Day 4

Place and routing (PnR) is performed using an abstract view of the GDS files generated by Magic. The abstract information will include metal and pin information. The PnR tool will use the abstract view information, formally defined as LEF information, to perform interconnect routing in conjunction to routing guides generated from the PnR flow.

#### An Introduction to LEF Files

1. Technology LEF - Contains layer information, via information, and restricted DRC rules
2. Cell LEF - Abstract information of standard cells

Tracks are used during the routing stage, routes can go over the tracks, or metal traces can go over the tracks. What the file is saying is that for the li1 layer the x or horizontal track is at an offset of 0.23 and a pitch of 0.46. The offset is the distance from the origin to the routing track in either the x or y direction. It is half the pitch so that means the tracks are centered around the origin.

#### Standard Cell Pin Placement
On-track standard cell pin placement is essential for DRC free PnR flow. Pins must align with the li1 and met1 preferred routing directions. During standard cell creation this concept must be accounted for. To ensure a cell is aligned with routing grids in Magic we can display a grid on top of the gds file.

To display the grid in magic:
![image](https://user-images.githubusercontent.com/127513356/224421388-f38cef44-6122-4697-b9fd-169dde0a2ec8.png)

#### LEF Generation in Magic
Magic allows users to generate cell LEF information directly from the Magic terminal. To generate the cell LEF file from Magic perform:
![image](https://user-images.githubusercontent.com/127513356/225734005-4d98c867-809e-4a22-84c8-a75810f7171e.png)

![image](https://user-images.githubusercontent.com/127513356/224421479-7d8d2eef-da45-48c3-84db-d9faa1e6fc3c.png)

Generated cell LEF file:

![image](https://user-images.githubusercontent.com/127513356/224421700-271db341-ccfb-4da5-895d-defb1a8ab5a8.png)

#### Including Custom Cells in OpenLANE

In order to include the new cells in OpenLANE we need to do some initial configuration:

1. Fully characterize new cell with GUNA for specified corners
2. Include cell level liberty file in top level liberty file
3. Reconfigure synthesis switches in the config.tcl file:


![image](https://user-images.githubusercontent.com/127513356/225739685-23a0ecd7-35f6-4136-909c-bc04f0ca30fa.png)

![image](https://user-images.githubusercontent.com/127513356/225738811-968cb1e7-ae4f-4269-b672-a2de2eaa4601.png)

![image](https://user-images.githubusercontent.com/127513356/225739153-32eca776-ca1f-48dc-99d5-0bdf894a3da4.png)

![image](https://user-images.githubusercontent.com/127513356/225739482-11cb9315-35be-4055-a570-9be2883ebeec.png)


![image](https://user-images.githubusercontent.com/127513356/225740201-a3551f8f-9310-4a26-a602-ab33a0271ff3.png)

1554 instances of custom inverter cell are added in design

Timing violation is verified by:
![image](https://user-images.githubusercontent.com/127513356/225740846-f6af69b8-2f34-4139-877d-b739a75e8350.png)


Next run floorplan and placement commands, and view def in magic:

c![image](https://user-images.githubusercontent.com/127513356/225742595-e8d6f21d-0716-4736-9a45-65b2fdf8626e.png)

![image](https://user-images.githubusercontent.com/127513356/225744643-0e0bff74-c235-4451-9dc5-5348dfb28a4c.png)

![image](https://user-images.githubusercontent.com/127513356/225745776-14d83ec6-1700-4779-8529-eabe9d4cf36b.png)

#### Post-CTS STA Analysis

Perform STA analysis on typical corner using openROAD. Use cts.netlist.
![image](https://user-images.githubusercontent.com/127513356/225758852-fa8efc3a-a94c-4b26-a761-a35a44970435.png)

![image](https://user-images.githubusercontent.com/127513356/225759528-d6716846-5220-4688-b70d-baa89b09334f.png)

![image](https://user-images.githubusercontent.com/127513356/225759372-bed741eb-914d-44cd-8c4d-4dd1d5fb275d.png)

### Routing | Day 5

Now that we are done with CTS, we start the routing stage by generating out power delivery network as per the pitch and width values from the track.info file.
To run it simply run below command in openlane shell:
gen_pdn
![image](https://user-images.githubusercontent.com/127513356/225761592-6ea1280e-4aeb-4fa4-9723-b7356bcee822.png)



Peform routing using the command run_routing. Once routing is complete, open DEF to see changes:
![image](https://user-images.githubusercontent.com/127513356/225763955-4cdf764e-22e1-481f-a155-1210fa253e24.png)

![image](https://user-images.githubusercontent.com/127513356/225764701-d376fc4b-df0c-48c4-b82d-f83767bca2cf.png)


![image](https://user-images.githubusercontent.com/127513356/225764826-64945243-c873-47f6-ba18-7a964f990972.png)

SPEF File obtained as an output of this stage -

![image](https://user-images.githubusercontent.com/127513356/225765001-b2b6609c-91d4-4f68-82b8-e29f25a2527a.png)


### Acknowledgements
Sincere thanks to following people for organizing and conducting this Physical Design workshop:

Kunal Ghosh, Nickson Jose, Dileesh Jostin
