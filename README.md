# LSSR_zephyr

Zephyr RTOS Tutorial using STM32 NUCLEO-WL55JC1 and Segger J-Link EDU Mini

## Install Zephyr RTOS

Follow the instructions for installing [Zephyr RROS Getting Started Guide](https://docs.zephyrproject.org/latest/develop/getting_started/index.html) up to the Build the Blinky Sample.

## Install J-Link for Segger

Download 64-bit DEB Installer at [J-Link Software and Documentation Pack](https://www.segger.com/downloads/jlink/#J-LinkSoftwareAndDocumentationPack)

Open downloads folder in terminal, then install J-Link software.

```sh
cd ~/Downloads
sudo apt-get install ./JLink_Linux_V786f_x86_64.deb
```

Connect the J-Link Mini EDU to the the STM32 Nucleo board via the debug header, then connect to a PC via J-link USB cable. Should have a stable green light.

To test it is working try:

```sh
JLinkGDBServer -if SWD -device STM32WL55JC
```

Agree to terms and tick box to not ask again today. You should get something like this confirming it's working.

```sh
J-Link is connected.
Firmware: J-Link EDU Mini V1 compiled Mar 28 2023 17:01:59
Hardware: V1.00
S/N: 801042479
Feature(s): FlashBP, GDB
Checking target voltage...
Target voltage: 3.30 V
Listening on TCP/IP port 2331
Connecting to target...
Halting core...
Connected to target
Waiting for GDB connection...
```

Close the connection with `ctrl + C`.

## Build the Blinky Sample

Firstly lets install a good terminal text editor:

```sh
# Copy      ctrl + C 
# Paste     ctrl + V
# Save      ctrl + S
# Exit      ctrl + Q
sudo apt-get install micro
```

Edit the `board.cmake` file to use J-Link.

```sh
micro ~/zephyrproject/zephyr/boards/arm/nucleo_wl55jc/board.cmake

```

This is what I changed my file to:

```sh
# SPDX-License-Identifier: Apache-2.0
board_runner_args(jlink "--device=STM32WL55JC" "--speed=4000")
include(${ZEPHYR_BASE}/boards/common/jlink.board.cmake)
include(${ZEPHYR_BASE}/boards/common/openocd.board.cmake)

```

Build the Blink sample:

```sh
cd ~/zephyrproject
source .venv/bin/activate
cd ~/zephyrproject/zephyr
west build -p always -b nucleo_wl55jc samples/basic/blinky
```

Flash to the Nucleo board:

```sh
west flash --runner jlink
```

If everything worked you see LED 2 flashing on the Nucleo board (〜^∇^)〜

Well done!

## Let's build our own Zephyr application

Create a new folder to save your application. Don't use any spaces in the folder name.

```sh
cd
mkdir LSSR_zephyr 
cd LSSR_zephyr
mkdir src
cd ~/zephyrproject/zephyr/samples/basic/blinky
cp CMakeLists.txt prj.conf ~/LSSR_zephyr
cp src/main.c ~/LSSR_zephyr/src
```

Edit the `CMakeLists.txt` file for the new project name:

```sh
cd ~/LSSR_zephyr
micro CMakeLists.txt
```

I changed my `project(blinky)` to `project(LSSR_zephyr)`

```sh
# SPDX-License-Identifier: Apache-2.0
cmake_minimum_required(VERSION 3.20.0)
find_package(Zephyr REQUIRED HINTS $ENV{ZEPHYR_BASE})
project(LSSR_zephyr)
target_sources(app PRIVATE src/main.c)
```

Save (`ctrl + s`) and quit (`ctrl + q`).

Make sure your virtual environment is still enabled

```sh
cd ~/zephyrproject
source .venv/bin/activate
source ~/zephyrproject/zephyr/zephyr-env.sh
```

Build the new application.

```sh
cd ~/LSSR_zephyr
west build -p auto -b nucleo_wl55jc
west flash --runner jlink
```

## Working in VSCode

```sh
code .
```

