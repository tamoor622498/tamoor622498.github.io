---
layout: post
published: true
title:  "Learning FPGAs With The Zybo Z7: Part 0 - Blink"
---
This is gonna be the start of my FPGA series. I plan to cover the board I’ve chosen, programming software, and creating a project. We will be creating “Blinky” which will just blink an LED. The project may seem simple, but it covers the basic workflow when working with an FPGA.

I ended up getting the Zybo Z7 7010 as the FPGA Development board to work with. Not only is it relatively low cost, it is also supported by the Xilinx Vivado toolchains. On top of this, it has a good amount of onboard I/O like switches and buttons. The board also comes with an SoC (System on a Chip). I plan to use this to practice with firmware development and just to get a wider understanding of FPGAs.

I was able to pick it up from an EBay listing for ~$100. But the [Digilent Shop](https://digilent.com/shop/zybo-z7-zynq-7000-arm-fpga-soc-development-board/){:target="_blank"} sells it for ~$200 with the academic price being ~$150.

### Xilinx Vivado
Vivado is a design suit that will allow us to not only write HDL ([Hardware Description Language](https://en.wikipedia.org/wiki/Hardware_description_language){:target="_blank"}) code with basic error checking, but will also allow us simulate, synthesize, implement, and generate a bit stream to program our board.

I chose to download Vivado 2019.2 and installed the WebPack, but a newer version will also work. Keep in mind, WebPack is rolled into the unlicensed version of Vivado for newer versions. The Zybo Z7 Board stuff can be found [here](https://digilent.com/reference/programmable-logic/zybo-z7/start){:target="_blank"} with the Xilinx Vivado download files [here](https://www.xilinx.com/support/download.html){:target="_blank"}.

### Creating a Project
Now that Vivado is installed, let's create a project. We aren’t gonna use any starter code and are just gonna create almost everything from scratch.

#### 1. Open vivado and click new project
![image](/pictures/zybo-part-zero/create-project.png)
I have a pre-existing project, you may not.
#### 2. Click next for the "Create a New Vivado Project"
![image](/pictures/zybo-part-zero/create-a-vivado-project.PNG)
#### 3. Name the project whatever you like, I chose “blinky”
![image](/pictures/zybo-part-zero/name-project.PNG)
#### 4. Select “RTL Project” and click next
![image](/pictures/zybo-part-zero/rtl-project.PNG)
#### 5. The “Add Sources” screen
![image](/pictures/zybo-part-zero/add-sources.PNG)
This where you can add any existing code files or directories. Also set is the targeted language, I’m going to be using SystemVerilog throughout this series, so choose verilog.
#### 6. I add constraints later, so click next on the constraints screen.
![image](/pictures/zybo-part-zero/constraints.PNG)
#### 7. The “Default Part” screen
![image](/pictures/zybo-part-zero/board-select.PNG)
Click “Boards” on the middle left and search for “Zybo Z7”. Click “Zybo Z7-10” if it comes up. If it doesn’t, click “Update Board Repositories” and see if that is able to add it to the list. If it doesn’t, you will have to manually add the files from the [Zybo Z7 Website](https://digilent.com/reference/software/vivado/board-files?redirect=1){:target="_blank"}.
#### 8. Click finish on the next screen.
![image](/pictures/zybo-part-zero/new-project-summary.PNG)
Now we have a project made.

### Project Files
A design source is where your actual programming for the FPGA logic is done. Depending on the project, there can be many design source files with each defining the logic for a small portion of the overall design.

A simulation file is similar, but a bit different than a design source. Instead of defining logic for components we are making, it defines logic for testing our work. This is where you would add in an external clock, any test output, or just anything else a component needs to be sufficiently tested. Because of this, simulation can have both [synthesizable and-non synthesizable logic](https://www.nandland.com/articles/synthesizable-vs-non-synthesizable-code-fpga-asic.html){:target="_blank"}.

Constraints files are the odd ones out. These define the physical attributes of an FPGA like the speed, I/O pins, LEDs, etc. You will rarely ever have to write a constraints file from scratch, they are usually obtained from the FPGA manufacturer. The Digilent Constraint files are available for download [here](https://github.com/digilent/digilent-xdc?_ga=2.163055676.1226470992.1643081413-1640342319.1639053765){:target="_blank"}. We will only need Zybo-Master.xdc.

### Creating/Adding Files to a Vivado Project
Now that we have a project set and know what each type of file is, we need to add them to our project. This process is just about the same for design source, simulation, or constraints files.

#### 1. Click the “+” button on the Sources panel
![image](/pictures/zybo-part-zero/plus-button.PNG)
#### 2. Select which type of file you would like to add
![image](/pictures/zybo-part-zero/add-sources-types.PNG)
#### 3. Click Add or Create file depending on which one you’re doing
![image](/pictures/zybo-part-zero/add-or-create-files.PNG)
To add a file, you would click add and navigate to the file to add it to your project. Otherwise, click Create.
#### 4. Choose file type and file name
![image](/pictures/zybo-part-zero/file-type-name.PNG)
This may look a bit different depending on if you're a design/simulation or constraints file. For a constraint file it will only list an option for XDC (Xilinx Design Constraints) files. For sources or design, it will let you pick a file corresponding to the HDL you would like to write. I’m using SystemVerilog for this tutorial.
#### 5. Click ok and then finish to add the file in
#### 6. Define Module
![image](/pictures/zybo-part-zero/define-module.PNG)
This is where you can choose the module name and pre-define any signals to and from. I tend to leave this as default and add them later.

### Files for Blinky
We have 3 files for blinky. The first one is blinky.sv, this holds the code for the logic to blink an LED and will run on the Zybo Z7 itself. The next is blinky_testbench.sv which is, unsurprisingly, the code for the testbench that will make sure our blinky.sv code works as expected. Just to make things clear, I’ve named this file as blinky_testbench, but the usual naming convention is blinky_tb. Finally we have the file that will require the least amount of work, Zybo-Z7-Master.xdc. This was downloaded and then imported into vivado from [the Digilent GitHub](https://github.com/digilent/digilent-xdc?_ga=2.163055676.1226470992.1643081413-1640342319.1639053765){:target="_blank"}.

### Zybo-Z7-Master.xdc Constraint File
As explained above, a constraints file outlines the physical attributes of the FPGA and let’s Vivado access them. For this project, we only want to blink a single LED on the Zybo Z7. Since it’s blinking, we will need a clock that will let the FPGA know when to turn the LED on and off. 

The Zybo-Z7-Master.xdc already has just about everything we need and the comments at the start tell us how we can use the file.
```
## This file is a general .xdc for the Zybo Z7 Rev. B
## It is compatible with the Zybo Z7-20 and Zybo Z7-10
## To use it in a project:
## - uncomment the lines corresponding to used pins
## - rename the used ports (in each line, after get_ports) according to the top level signal names in the project
```
Just uncomment the line needed and edit the command to whatever desired.

We will first set the clock by uncommenting two lines below `##clock`. This can be done by removing each # in front of each line.
```
##clock
set_property -dict { PACKAGE_PIN K17   IOSTANDARD LVCMOS33 } [get_ports { sysclk }]; #IO_L12P_T1_MRCC_35 Sch=sysclk
create_clock -add -name sys_clk_pin -period 8.00 -waveform {0 4} [get_ports { sysclk }];
```
The first line grabs the clock pin and defines it to be `sysclk`. The second line uses the `create_clock` command to start a clock on the `sysclk` pin. The `-period 8.00` modifier is setting the period to 8ns (nano seconds) and `-waveform {0 4}` defines the duty cycle. The duty cycle is the percentage of the period that's high. The clock defined above has a duty cycle of 50% since the signal will be high from 0ns to 4ns of the period and low for the rest. We can define a faster clock by shortening the period and shift the duty cycle by editing the waveform numbers. More detailed information can be found in the [Xilinx Vivado Design Suite User Guide: Using Constraints](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2012_2/ug903-vivado-using-constraints.pdf){:target="_blank"}.

We will next define the LED pin. Once again, the constraints file makes this extremely easy for us. We only need 1 LED, so uncomment the first line under `##LEDs`.
```
set_property -dict { PACKAGE_PIN M14   IOSTANDARD LVCMOS33 } [get_ports { led0 }]; #IO_L23P_T3_35 Sch=led[0]
```
The only edit made to this line is `led[0]` was changed to `led0`. This way we can treat the LED as a pin rather than an array of pins.

Now we are done with the constraints file!

### blinky.sv
This is the file that will run on the FPGA itself. It will contain SystemVerilog code to define the behavior of the FPGA. This is a simple project, so the entire file is
```SystemVerilog
`timescale 1ns / 1ps

module blimky(
    input logic sysclk,
    output logic led0,
    output logic [3:0] count
    );
    
    logic [3:0] count_d = 0;

    always @(posedge sysclk)
    begin
        count_d = count_d + 1;
    end
  
    assign count = count_d;
    assign led0 = count_d[3];
    
endmodule //blimky
```
Yes, I know. I misspelled blinky to blimky, but I kept it because I thought it was funny. You can name it whatever you want as long as you keep up the convention.

One of the biggest hurdles for getting into FPGAs is having a good understanding of the HDL (hardware description language) used. They are all similar enough so that if you understand one, it’ll be a lot easier to pick up the others. I chose SystemVerilog because I’ve had some exposure to it in the past and I quite liked how it worked. So let’s get started in breaking down this bit of code.

The first line is ` `timescale 1ns / 1ps`. This essentially defines how time is interpreted throughout the rest of the program. Timing is extremely important in almost all digital systems. When we set time like this, it defines what commands like `#1` or `#100` mean. More detailed information can be found [here](https://www.chipverify.com/verilog/verilog-timescale){:target="_blank"}.

```sv
module blimky(
    input logic sysclk,
    output logic led0,
    output logic [3:0] count
    );
    ...
endmodule //blimky
```
