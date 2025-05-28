# 简化为3自由度的空间鼠标

简化了设计，只用一个摇杆和一个编码器实现3个转动自由度，需要搭配3D connextion软件使用，不抢鼠标

【手搓一个空间鼠标】 https://www.bilibili.com/video/BV1HwE4zrEH5/?share_source=copy_web&vd_source=4f14277a41ad80f6b36edd713a9a2882

### 🧾 物料清单

| 序号 | 名称               | 数量         |
|------|--------------------|--------------|
| 1    | Arduino Pro Micro  | 1 个         |
| 2    | 游戏手柄摇杆       | 1 个         |
| 3    | 编码器             | 1 个         |
| 4    | 硅胶线             | 若干         |




![空间鼠标](https://github.com/user-attachments/assets/0c254c2a-c071-4fe1-9a34-733a87cde590)



## Features of the 3 DOF mouse
- Source code for an Arduino Pro Micro to read joysticks and calculate the kinematics
- Emulation of the USB identification and the HID interface to behave like an original space mouse
- An encoder wheel can be used to replace Z axis
 
## Macro-Pad for CAD users
The original 3Dconnexion windows driver is very elegant to detect which programm you are running and offering custom actions for the keys on a space mouse. You can utilize this behavior and build a space mouse with keys, just without the central part, the space mouse itself. You are left with keys, that you can assign to actions for your CAD programm. This comes handy, if your original spacemouse doesn't have enough keys. In this case your PC will see an additional mouse where only the keystrokes are send and evaluated.

# Getting Started with PlatformIO
You can use PlatformIO to flash the board with this fast steps.
PlatformIO is easier than ArduinoIDE, because you don't need to change the board.txt files there.

1. Install [PlatformIO](https://platformio.org/).
2. Clone this repo into a folder (hint for windows: network locations are not recommended)
3. Copy and rename `spacemouse-keys/config_sample.h` to `spacemouse-keys/config.h` and change the values to whatever suits.
4. Open PlatformIO / vscode and open the newly created folder.
5. Click on the upload arrow in the IDE in the status bar or run `pio run -t upload`.
6. [Assign the pins of the joysticks and go through the calibration](#calibrate-your-hardware)
6. [Use your space mouse](#use-the-6-dof-mouse)
8. Done!

# Getting Started with Arduino IDE 
1. [Create a custom board](#custom-board-to-emulate-the-space-mouse) in your Arduino IDE, that emulates the original space mouse
2. [Download or clone this github repository](#cloning-the-github-repo)
3. [Rename the config_sample.h to config.h](#create-your-own-config-file)
4. [Try to compile and flash your board](#compiling-and-flashing-the-firmware)
5. [Assign the pins of the joysticks and go through the calibration](#calibrate-your-hardware)
6. [Use your space mouse](#use-the-6-dof-mouse)
7. Done!
   
## Custom board to emulate the space mouse
The boards.txt file needs an additional board definition, which tells the microprocessor to report the USB identifiers correctly and emulate the 3dconnexion space-mouse. Please follow the detailled instructions on the [wiki page about custom board definition](https://github.com/AndunHH/spacemouse/wiki/Creating-a-custom-board-for-Arduino-IDE). 

## Cloning the github repo
Clone the github repo to your computer: Scroll-Up to the green "<> Code" Button and select, if you wish to clone or just download the code.

## Create your own config file
Copy the config_sample.h and rename it to config.h.
This is done to avoid the personal config file being overwritten when pulling new updates from this repository. You probably have to update the config.h file with new additions from the config_sample.h, but your pin assignment will not stay.

## Compiling and flashing the firmware
- Open the Arduino IDE (1.8.19 and 2.3.2 are tested on Ubuntu).
- Open spacemouse-keys.ino
- Select Tools -> Board -> SparkFun AVR Boards -> Spacemouse.
- (If you followed another boards.txt instructions, which also allow 3.3V with 8 Mhz: Make sure to select the proper processor: 5V 16MHz)
- Select the correct port (see troubleshooting section, which might be necessary for first upload)
- Compile the firmware

### Troubleshooting uploading
Troubleshooting for uploading is explained in detail here: 

[Uploading firmware to the Pro Micro](https://github.com/AndunHH/spacemouse/wiki/Uploading-firmware-to-the-Pro-Micro)


# Calibrate your hardware
After compiling and uploading the programm to your hardware, you can connect via the serial monitor. In the upper line, you can send the desired debug mode to the board and observe the output. "-1" stops the debug output.

Read and follow the instructions throughout the config.h file and write down your results. Recompile after every step.

This calibration is supported by various debug outputs which can toggle on or off before compiling or during run time by sending the corresponding number via the serial interface.
All debug outputs are described at the top of your config_sample.h.

1. Check and correct your pin out -> Refer to the pictures in the [Electronics](#electronics) section below.
2. Tune dead zone to avoid jittering
3. Getting min and max values for your joysticks
	- There is a semi-automatic approach, which returns you the minimum and maximum values seen within 15s.
4. Adjust sensitivity
5. Choose modifier function:

Choose a modifier function with the help of the following picture. Note, that the Squared, Squared Tan and Cubed Tan function act like a deadzone filter, because small inputs are resulting in nearly zero output, which may reduced unwanted movements. 
Also note, that some of those functions are already at their maximum output (and therefore limited), before the input reaches 350.
![picture illustrating the different modifier functions](pictures/modifierFunctions.svg)


# Use the 6 DOF mouse
## Download the 3dconnexion driver on windows and mac
You will also need to download and install the [3DConnexion software](https://3dconnexion.com/us/drivers-application/3dxware-10/)

If all goes well, the 3DConnexion software will show a SpaceMouse Pro wireless when the Arduino is connected.

# Software Main Idea
1. The software reads the eight ADC values of the joy stick and encoder
2. During start-up the zero-position of the joystick is measured and subtracted from the adc-value. -> The values now range from e.g. -500 to +500
3. A dead zone in the middle is applied to avoid small noisy movements. (E.g. every value between +/- 3 is fixed to zero)
4. The movement of the joysticks is mapped from the original about ca. +/- 500 digits to exactly +/- 350. (Therefore the real min and max values will be calibrated) Now all further calculations can be done with this normalized values between +/-350.
5. We calculate the rotation based on this.
6. Sending the velocities and keys to the PC, see also  [SpaceNavigator.md](SpaceNavigator.md) for further details about the emulated USB HID.

# Printed parts

https://makerworld.com.cn/zh/models/1229254-3dkong-jian-shu-biao#profileId-1309785

# See also

Here are some other projects with regard to space mice. The arrow indicates what is emulated. 

* [This repository itself](https://github.com/AndunHH/spacemouse) -> space mouse
* [DIY SpaceMouse Profiles](https://github.com/raulpetru/DIY_SpaceMouse_Profiles) -> User interface to tune e.g. sensitivity of the mouse
* [A 6 degrees of freedom mouse for CAD programs using cheap linear Hall Effect sensors for position measurement](https://github.com/ChromeBee/Hall-Effect-Sensor-CAD-Mouse-Spacemouse) -> space mouse
* [DIY Spacemouse for Fusion 360](https://github.com/sb-ocr/diy-spacemouse) -> Mouse and Keyboard
* [spacemouse with an esp32](https://github.com/horvatkm/space_mouse_esp32s2) -> work in progress -> space mouse pro
* [Space Fox](https://github.com/pepijndevos/spacefox) -> Joystick based on potentiometers
* [Orbion The OpenSource 3D Space Mouse](https://github.com/FaqT0tum/Orbion_3D_Space_Mouse) -> Mouse and Keyboard
* 



# License
Because TeachingTech published his source code on Printables under this license, it also applies here:

[![CC BY-NC-SA 4.0][cc-by-nc-sa-shield]][cc-by-nc-sa]

This work is licensed under a
[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License][cc-by-nc-sa].

[![CC BY-NC-SA 4.0][cc-by-nc-sa-image]][cc-by-nc-sa]

[cc-by-nc-sa]: http://creativecommons.org/licenses/by-nc-sa/4.0/
[cc-by-nc-sa-image]: https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png
[cc-by-nc-sa-shield]: https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg
