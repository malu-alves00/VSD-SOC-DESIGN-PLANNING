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
- Discussed Sky130 DRC rules and tested them in Magic

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

### Labs - DRC

Image 16 - Opening met3 in Magic an using command 'drc why' to check DRC on rule met3.6
<img width="1920" height="1014" alt="day3-drcwhy36" src="https://github.com/user-attachments/assets/f4d8a11e-5e3e-4a5e-8d13-0c867a65ebc9" />

Image 17 - Creating layer of m3contact and using 'cif see VIA2'
<img width="1920" height="1014" alt="day3-via2" src="https://github.com/user-attachments/assets/d723cca3-30df-435a-b47a-2b66b7d816eb" />

Image 18 - Poly9 rule not getting flagged, need changes on the tech file (check uploaded sky130a.tech file on Day 3 directory)
<img width="1920" height="1014" alt="day3-notshowingpoly9" src="https://github.com/user-attachments/assets/28e38cfd-f8cf-4c09-9e7f-9b1ebf0295c1" />

Image 19 - Poly9 rule getting flagged correctly after adding sections to the DRC
<img width="958" height="1005" alt="day3-firstpoly9detected" src="https://github.com/user-attachments/assets/818de37e-e4cc-4cee-ac5e-6117b9c6e7f9" />

Image 20 - Analysing more examples of when the poly9 should be applied with pdiffusion and ndiffusion layers
<img width="1920" height="1014" alt="day3-polymorerules" src="https://github.com/user-attachments/assets/15be767e-484f-4a1d-8dab-748ff4b02a91" />

Obs.: I've been struggling a lot with visual bug on the Magic tool used in the virtual machine, often needing to restart it multiple times. Some indications wouldn't appear in the red poly layers even though it would be indicated on the 'drc why', so I considered it to be part of the visual bugs.

Image 21 - Analysis and implementation of the nwell.6 rule according to the lecture
<img width="1920" height="1014" alt="day3-deepnwellrule" src="https://github.com/user-attachments/assets/a367ca87-987a-43e8-8353-dc21e6419bf4" />

Image 22 - Lab exercise to correct transistor channel width DRC (difftap.2)
<img width="1920" height="1014" alt="day3-exercise" src="https://github.com/user-attachments/assets/bfe98af8-326d-4cbc-a4d7-3a2ca40f8ae8" />

## Day 4

The following topics are addresed:
- Creation of LEF file of the previously opened inverter to be used on the PicoRV32 design
- Analysis of the grid layout in Magic
- How to make the CTS power aware? Using logic gates that can block the clock from propagating. However, the delay needs to be studied by a delay table
- FFs need a setup time depending on the combinational logic present. They are also prone to uncertainty of the clock and jittering, which should be calculated and taken into account. Based on the characteristics of the circuit, the clock needs to be calculated thinking of the uncertainty and delays
- A good strategy to route a clock tree is using the H-tree approach, as it's able to balance the clock time and is good for scaling designs
- Crosstalk of the signals should be avoided, and the clock net shielding is good to remove internal capacitances that might affect the signals
- Since there are many buffers in the clock tree, the time required for the clock to reach from a launch FF to a capture FF needs to take those middle buffer delays in consideration
- Examples of setup and hold analysis using real clocks were shown and discussed

### Lab

Image 23 - Tracks info used in routing stage
<img width="1920" height="1014" alt="day4-tracksinfo" src="https://github.com/user-attachments/assets/285d2d7d-0631-4ec6-a5bf-f439b8efad1c" />

Image 24 - Zooming in to inverter layout to see current grids
<img width="958" height="1005" alt="day4-gridsactivated" src="https://github.com/user-attachments/assets/632d4f59-026e-4d0a-8eb3-9f797920e3f6" />

Image 25 - Setting the grid as the size described on the tracks info file. Note that the width and height of the cell are whole multiples of the width and height of the tracks
<img width="958" height="1005" alt="day4-gridsastracksfile" src="https://github.com/user-attachments/assets/e68bc6e8-b4f0-4856-926a-dd451d04319b" />

Image 26 - Also note that the contacts are localized within the intersection of the tracks
<img width="958" height="1005" alt="day4-contactsintersection" src="https://github.com/user-attachments/assets/c44c6434-a5e3-40b6-bc7e-1f7b06ccc297" />

Image 27 - Here, a copy of the file sky130_vsdinv.mag is saved, and a LEF file is created by using the 'lef write' command.
<img width="958" height="1005" alt="day4-leffile" src="https://github.com/user-attachments/assets/89ec93c8-ff2f-4c05-83af-e14ff2758964" />

Image 28 - The LEF file, along with the libs, are copied to the src folder of the picorv32 design
<img width="958" height="1005" alt="day4-copytopico32" src="https://github.com/user-attachments/assets/149cf90c-38db-41c9-94b3-91b911e75119" />

Image 29 - Setting the lib files on config.tcl of the PicoRV32 design
<img width="1920" height="1014" alt="day4-configforlef" src="https://github.com/user-attachments/assets/84fe5e42-a1cd-41b7-a486-8089e37c7b3c" />

Image 30 - Selecting our current run and setting lefs
<img width="958" height="1005" alt="day4-setlefs" src="https://github.com/user-attachments/assets/eaa26e98-f632-4576-a408-ea5e27ac3eb1" />

Image 31 - We run synthesis using the command 'run_synthesis'. When it's finished, it's possible to see the chip area, wns (worst timing violation) and tns (total negative slack)
<img width="1920" height="1014" alt="day4-areatimingsynthesis" src="https://github.com/user-attachments/assets/e08f3c9b-5ca7-4a8d-a892-a40c0e633df9" />

Those timing values aren't ideal, so we'll change some variables to see how the output changes. Using 'set ::env[VARIABLE_NAME] value' and 'echo $::env[VARIABLE_NAME]', we:
- Set SYNTH_STRATEGY DELAY 1
- Verify that SYNTH_BUFFERING is 1 (enable)
- Set SYNTH_SIZING from 0 to 1 (enabled)
- Verify that SYNTH_DRIVING_CELL is set to sky130_fd_sc_hd__inv_8

To run synthesis again, we need to overwrite the design using prep -design picorv32a -tag (DATE) -overwrite. Don't forget to use the date you ran the synthesis! The setting lefs step in image 30 is also done, again.

Image 32 - Timing improvement and bigger area are seen as we do 'run_synthesis' again
<img width="1920" height="1014" alt="day4-besttiming" src="https://github.com/user-attachments/assets/c6083567-917e-4b2e-82f0-142723944a10" />

Image 33 - While 'run_floorplan' is executing, we open the merged.lef file for the PicoRV32 and check that our inverter design is correctly referenced
<img width="958" height="1005" alt="day4-leffile" src="https://github.com/user-attachments/assets/a5163c38-2b69-4c73-820c-c234ee6a5846" />

Image 34 - Uh oh, some errors during floorplan. Instead, we try the approach to use the commands 'init_floorplan', 'place_io', and 'tap_decap_or' separately, which are contained within the 'run_floorplan'
<img width="1920" height="1014" alt="day4-floorplanerror" src="https://github.com/user-attachments/assets/5518a4fa-e113-47ca-8eae-bf97ecc8ed33" />

Image 35 - When it runs correctly, I execute 'run_placement' and open the resulting def file using magic
<img width="1920" height="1014" alt="day4-newplacement" src="https://github.com/user-attachments/assets/985d0f98-bdc5-4f2b-bc68-22599c1aa98c" />

Image 36 - It's possible to find the new inverter cell in the layout
<img width="1920" height="1014" alt="day4-newcellmagic" src="https://github.com/user-attachments/assets/e8588a9d-3228-40e0-96d8-a014c9bed4b2" />

Image 37 - Selecting the option "expand" on the "cell manager" window, it's possible to see the connections
<img width="1920" height="1014" alt="day4-expandcells" src="https://github.com/user-attachments/assets/add1f247-3368-4ffd-bd24-f77cef456512" />

Now, we do another synthesis to generate new files that don't have the preferred timings, as seen by this last synthesis

Image 38 - Create a pre_sta.conf on the openlane folder
<img width="1920" height="1014" alt="day4-pre_sta" src="https://github.com/user-attachments/assets/9b20fee5-6c3b-417a-9831-2d9985ca6686" />

Image 39 - Create a my_base.sdc file on the src folder of the PicoRV32 design
<img width="1920" height="1014" alt="day4-my_base" src="https://github.com/user-attachments/assets/7598d6b6-4ed4-4331-b16f-31571377b10e" />

Image 40 - On the openlane folder, run 'sta pre_sta.conf' to see timing violations
<img width="1920" height="1014" alt="day4-slackviolated" src="https://github.com/user-attachments/assets/eb87b3d3-a297-4285-a419-c7a01047bae1" />

Image 41 - Seeing how high it is, we set the SYNTH_MAX_FANOUT var to 4, overwrite the design file, 'run_synthesis' again and run the 'sta pre_sta.conf' to see the changes. It's still bad, so we analyze the cells to identify the causes
<img width="958" height="1005" alt="day4-afterslack" src="https://github.com/user-attachments/assets/e61f7f8f-b473-4a7a-afed-f37b8e28dcd5" />

Image 42, 43 - It's possible to see some OR cells with 4 fanouts. As a way to make this better, we need to run some report commands. By reporting the net using 'report_net -connections _11672', we see the driver pins. The example below is for the _14510, but I also identify some more.
<img width="958" height="1005" alt="day4-4fanout" src="https://github.com/user-attachments/assets/d6de1065-233e-4209-87b4-a7a980f8cf12" />
<img width="958" height="1005" alt="day4-reportconnections" src="https://github.com/user-attachments/assets/3da5d7ad-b92d-478c-8f1f-939628ed28ce" />

To try and fix the slack, we replace some cells with better suited ones for driving 4 fanouts using the command 'replace_cell [driver number] sky130_fd_sc_hd__or(number)_4'

Image 44 - After a long time replacing OR cells, the slack had gone up by ~1s
<img width="958" height="1005" alt="day4-finalorslack" src="https://github.com/user-attachments/assets/6658c63b-84ec-468a-bb08-bd7b4c4178ff" />

Image 45 - report_checks -from _29052 -to _30440 -through _14510
<img width="958" height="1005" alt="day4-customreport" src="https://github.com/user-attachments/assets/44fc9d32-9829-45bf-97c4-e8dc11cf2b59" />

Now, we write those changes in the netlist with the command 'write_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-07_21-48(or any date that the flow ran)/results/synthesis/picorv32a.synthesis.v'

Image 46 - Here, we can find the commands related to the flow. Inside the cts.tcl, it's possible to see the procs and list of commands inside
<img width="958" height="1005" alt="day4-commandslist" src="https://github.com/user-attachments/assets/178c2c50-2785-4ced-95cf-8022bf012b2c" />

"Image 47" - In this step, we run the 'openroad' command do to post-CTS timing analysis using OpenROAD. As I forgot to take this screenshot before moving on, check image 50. The only difference is the 'pico_cts.db' instead of 'pico_cts1.db'

Image 48 - This time, we'll remove the clkbuf_1 from the list
<img width="1920" height="1014" alt="day4-clockbuffdel" src="https://github.com/user-attachments/assets/9db2f1ea-90d8-4199-9170-690a71ddaf6a" />

Image 49 - We again do 'run_cts' to see the results, but an error pops up because we need to reference the placement def, not the cts one. So, using the commands in the image, we correct it
<img width="1920" height="1014" alt="day4-changingdeftocorrect" src="https://github.com/user-attachments/assets/06a39068-0c3c-47b0-ac4f-40e8379a58bd" />

Image 50 - To do the reports, as the files were changed, the db needs to be written again
<img width="1920" height="1014" alt="day4-commandsandreportlasttime" src="https://github.com/user-attachments/assets/06982db0-f1f0-4635-807c-a51aa4ae8c51" />

Image 51 - Hold slack from report
<img width="1920" height="1014" alt="day4-newholdslack" src="https://github.com/user-attachments/assets/7b5c33ae-5add-495d-ab83-9a377c519929" />

Image 52 - Setup slack from report
<img width="1920" height="1014" alt="setupslacklast" src="https://github.com/user-attachments/assets/4ef61113-ed12-410a-8e9a-736deec3238b" />

Image 53 - Checking clock skew for setup and hold, and adding back the clkbuf_1
<img width="1920" height="1014" alt="day4-skewsandclkbf" src="https://github.com/user-attachments/assets/146c3939-2998-4ef1-9fa0-825137931443" />



