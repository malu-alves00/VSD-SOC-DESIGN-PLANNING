# VSD SoC Design and Planning Workshop
This is a workshop offered by [VLSI System Design](https://www.vlsisystemdesign.com/) with the intent of developing skills relating to - and understanding of - the place and route process os chip design.
As tools, [Google SkyWater Sky130 PDK](https://skywater-pdk.readthedocs.io/en/main/) and [Openlane](https://efabless-com.translate.goog/openlane) flow are used.

## Day 1 - Introduction, Openlane, Sky130A PDK and synthesis

The following topics are addresed:
- Basics of how digital systems create a communication between hardware and software
- RTL2GDS flow
- Openlane flow - how the integrated tools are used for each step in IC design, directory structure, configuration files, design preparation and synthesis

### Lab

Image 1 - Steps for preparing design (in this case, PicoRV32) and synthesis as commands on the terminal.

![alt text](https://github.com/malu-alves00/VSD-SOC-DESIGN-PLANNING/blob/main/Day%201/day1-synthesis.png?raw=true "Synthesis")

Image 2 - Calculating flop ratio by analyzing synthesis report. Final ratio of 10.842% ((1613 / 14876) * 100%)

![alt text](https://github.com/malu-alves00/VSD-SOC-DESIGN-PLANNING/blob/main/Day%201/day1-flopratio.png?raw=true "Flop Ratio")

To check synthesized netlist: see openlane/designs/picorv32a/runs/(date)/results/synthesis/picorv32a.synthesis.v

To check Yosys and OpenSTA reports: openlane/designs/picorv32a/runs/(date)/reports/synthesis/

## Day 2 - Floorplanning and placement

The following topics are addresed:

Floorplanning
- Calculating utilization factor (area occupied by netlist / total area of core) and aspect ratio of the core (height/width)
- Defining pre-placed cells location, which are placed before standard cells
- Usage of decoupling capacitor to counter fluctuation of power delivered to cells and noise, which can cause undefined logic values and unstable circuit functioning
- Power planning to provide power to cells

Placement
- Bind netlist to physical cells and determine size. On library, one can find same cells of different sizes depending on the needs of the circuit
- Place the netlist on the core, respecting preplaced cells and finding the best locations to guarantee signal integrity and physical proximity to where the signals should travel
- Place repeaters to strenghten signals of cells that are distant

Cell design flow
- Standard cells are kept at a library, such as AND, inverter, buffer, etc
- For inputs of the flow: PDKs, DRC and LVS rules, SPICE models, library and uuser defined specs
- It has some design steps: circuit design, layout and characterization
- Then, for output, we get circuit description language (CDL) files, GDSII, LEF, extracted SPICE netlist (.cir), timing, noise, power libs and function
- For the characterization flow, the cell's behavior should be analyzed under various conditions as to create implementation models

Timing characterization
- One should note the various timing threshold definitions, such as the slew, how much time the cell takes to respond to an input and the corresponding outputs
- The propagation delay is also crucial to compare how the output in a cell behaves to an input, as the different delays in communicating cells can cause unexpected results if not properly addressed
- Transition time points at the slew time of inputs and outputs, calculated using the threshold values
- The waveforms are a way for visualization of this step


### Lab

Initiate floorplan in the terminal using the command 'run_floorplan'

Image 3 - You can check the variables used in each stage in openlane/configuration/README.md
<img width="1920" height="1014" alt="day2-variables" src="https://github.com/user-attachments/assets/dd3a8771-b799-4b08-8848-b3c6af853865" />

Image 4 - You can check the floorplan parameters in openlane/configuration/floorplan.tcl, as seen below. Those parameters are overriden by the openlane/designs/picorv32a/config.tcl, which are, again, overriden by the Sky130 pdk specifications. We then understand that the pdk has the highest preference when specifying those parameters.
<img width="1920" height="1014" alt="day2-floorplanconfig" src="https://github.com/user-attachments/assets/a656fef1-0bce-4479-8709-82aa8b85e682" />

Image 5 - Opening layout with Magic
<img width="1920" height="1014" alt="day2-floorplanmagic" src="https://github.com/user-attachments/assets/6315527b-8c52-4b2b-88b0-d03509103fa2" />

Image 6 - Poor stdcells squished at the bottom left of the floorplan, waiting for their demise on placement
<img width="1920" height="1014" alt="day2-floorplanstdcells" src="https://github.com/user-attachments/assets/46c54827-051e-4493-9ed1-2267df531aae" />

Image 7 - Decaps, taps and IO pins
<img width="1920" height="1014" alt="day2-floorplanzoom" src="https://github.com/user-attachments/assets/c4280bd8-47f0-4e4c-b28f-1a18a544f023" />

Initiate placement using RePLACE, command 'run_placement'.

Image 8 - Visualizing placement result with Magic
<img width="1920" height="1014" alt="day2-placementmagic" src="https://github.com/user-attachments/assets/3b928ab9-b4fa-4378-9a29-7e4554f98a15" />

Image 9 - Legal placement of the cells
<img width="1920" height="1014" alt="day2-placementlegal" src="https://github.com/user-attachments/assets/88099206-591e-43e3-b3ec-03b295dcc13e" />

## Day 3 - Designing library cell

The following topics are addresed:
- It's possible to change variables when it's needed during Openlane flow using the commmand 'set (variable name as shown in the list addressed on day 1) (value)'
- Creation of the SPICE deck, with information about component connectivity and values. With that, it's possible to create SPICE simulations to see the waveform behavior of the component. During this step, the different behaviors of the waveforms of an inverter were shown according to the size differences of width/length of PMOS and NMOS, and the resulting switching thresholds were discussed
- The steps of the 16-mak CMOS process were explained in details
- Reviewed [Standard cell design and characterization using openlane flow](https://github.com/nickson-jose/vsdstdcelldesign) repository on GitHub and analysed the inverter

### Labs

Image 10 - Opening and understanding the inverter layout using Magic
<img width="1920" height="1014" alt="day3-inverterlayout" src="https://github.com/user-attachments/assets/acdf5e47-9437-4b37-982b-3fa862668991" />

Image 11 - Deleting part of the design to see DRC in action
<img width="1920" height="1014" alt="day3-testingdrc" src="https://github.com/user-attachments/assets/b707d432-131a-4c54-9539-75a2018c8e11" />

Image 12 - After this, we extracted the .mag file into a SPICE netlist using the commands 'extract all', 'ext2spice cthresh 0 rthresh 0' and 'ext2spice' on the Magic command interface and edited the file for simulation analysis (I forgot to get a screenshot of the file before getting edited)
<img width="1920" height="1014" alt="day3-extractedspice" src="https://github.com/user-attachments/assets/d99d7e7a-6d56-479d-a925-413ff779df71" />

Image 13 - Simulating and plotting waveform using ngspice
<img width="1920" height="1014" alt="day3-spicesim" src="https://github.com/user-attachments/assets/58c4699b-c85e-4cf4-af3c-256a171f7d3f" />

Images 14, 15 - Finding 20% and 80% rise values, respectively (y1 = 0.66 and y2 = 2.64)
<img width="1920" height="1014" alt="day3-20pctrise" src="https://github.com/user-attachments/assets/c6d15728-2dc8-40de-8f02-a72cf1058a25" />
<img width="1920" height="1014" alt="day3-80pctrise" src="https://github.com/user-attachments/assets/1e7ae3e3-d7b2-4425-a285-63ffd355326a" />




