# RN4870_Remote_Configuration
This note describes the procedure required to demonstrate the RN4871 remote command feature. This feature allows the control of an RN4871 module without the use of a host microcontroller remotely from either another RN4871 module or an Android app.

<!--- This document is written by Raghu (Microchip Tech) to explain the use to the Remote configuration feature in the RN4871 modules.) -->

The remote command feature works by using a special BLE GATT service that emulates a wired serial connection called transparent UART mode. This combined with a unique command set allows remote functionality of the module.

The remote command feature can be controlled by either another RN4871 module, a competitors BLE module or an Android app.

For the first demonstration two RN4871 modules will be used. One will be called the Master Module the other the Remote Module. The Master Module is connected to the PC and is in control of the Remote Module. The Remote Module is connected to the PC for configuration and status feedback only.

We will configure the Remote Module first, so when we get to the connection sequence on the Master module, it can connect properly

The step by step procedure to configuration is provided below. Each step shows what command to issue to the RN4871 module in the format of - *TX: command*.

The expected response for each Tx command sent, from the RN4870/71 module in the format of - *RX: response*.

1. Enter command mode by sending $$$
    - TX: *$$$*
    - RX: *CMD>*
2. Set to factory defaults by sending “SF,1 <`CR`>”
    - TX: *SF,1<`CR`>*
    - RX: *Reboot after Factory Reset<`CR`><`LF`>%REBOOT%*
3. Enter command mode by sending $$$
    - TX: *$$$*
    - RX: *CMD>*
4. Set name of module to “Remote” by sending “SN,Remote<`CR`>”
    - TX: *SN,Remote<`CR`>*
    - RX: *AOK<`CR`><`LF`>*
5. Enable device information and transparent UART operation by sending “SS,C0<`CR`>”
    - TX: *SS,C0<`CR`>*
    - RX: *AOK<`CR`><`LF`>*
6. Enable authentication/security by sending “SA,2<`CR`>”
    - TX: *SA,2<`CR`>*
    - RX: *AOK<`CR`><`LF`>*
7. Set 4-digit PIN code by sending “SP,1234<`CR`>”
    - TX: *SP,1234<`CR`>*
    - RX: *AOK<`CR`><`LF`>*
8. Save and reboot by sending “R,1<`CR`>”
    - TX: *“R,1<`CR`>*
    - RX: *AOK<`CR`><`LF`>*

The above steps complete the configuration for the RN4871 Remote Module. Next the RN4871 Master Module is configured as outlined below.

1. Enter command mode by sending $$$
    - TX: *$$$*
    - RX: *CMD>*
2. Set to factory defaults by sending “SF,1 <`CR`>”
    - TX: *SF,1<`CR`>*
    - RX: *Reboot after Factory Reset<`CR`><`LF`>%REBOOT%*
3. Enter command mode by sending $$$
    - TX: *$$$*
    - RX: *CMD>*
4. Set name of module to “Remote” by sending “SN,Master<`CR`>”
    - TX: *SN,Master<`CR`>*
    - RX: *AOK<`CR`><`LF`>*
5. Enable device information and transparent UART operation by sending “SS,C0<`CR`>”
    - TX: *SS,C0<`CR`>*
    - RX: *AOK<`CR`><`LF`>*
6. Enable authentication/security by sending “SA,2<`CR`>”
    - TX: *SA,2<`CR`>*
    - RX: *AOK<`CR`><`LF`>*
7. Set 4-digit PIN code by sending “SP,1234<`CR`>”
    - TX: *SP,1234<`CR`>*
    - RX: *AOK<`CR`><`LF`>*
8. Save and reboot by sending “R,1<`CR`>”
    - TX: *“R,1<`CR`>*
    - RX: *AOK<`CR`><`LF`>*
9. Enter command mode by sending $$$
    - TX: *$$$*
    - RX: *CMD>*
10. Enter command to connect to the Remote Module by sending  “C,0,<`BT Address`><`CR`>”
    - TX: *C,0,12345678912<`CR`>*
    - RX: *Trying<`CR`><`LF`>%CONNECT,0,3481F407DB41%%STREAM_OPEN%*
11. Enter command mode by sending $$$
    - TX: *$$$*
    - RX: *CMD>*
12. Issue the Bond command by sending “B<`CR`>”
    - TX: *B<`CR`>*
    - RX: *AOK<`CR`><`LF`>CMD> %SECURED%BONDED*
13. Enter remote command mode by sending “!,1<`CR`>”
    - TX: *!,1<`CR`>*
    - RX: *RMT>*

Congratulations! You are now in Remote Command mode of the RN4871 Remote module.

The next step is to check the control over the RN4871 Remote module. Here are a few commands and the expected responses.

### Example 1:
In this example we will ask the RN4871 Remote module to “Dump” its information which will return the MAC address, the modules name, connections status, security setting, enabled features and enabled services.

Send the “Dump” command by sending “D<`CR`>”

Expected response:

*BTA=3481F407DB41<`CR`><`LF`>*\
*Name=Remote<`CR`><`LF`>*\
*Connected=3481F406DDD9,0<`CR`><`LF`>*\
*Authen=2<`CR`><`LF`>*\
*Features=0000<`CR`><`LF`>*\
*Services=C0<`CR`><`LF`>*\
*RMT>*\

### Example 2:

In this example we will ask the RN4871 Remote module for its name which will return the name of the module it was configured with. This can also be changed using the SN,”name” command.

Send the “Get name” command by sending “GN<`CR`>”

Expected response:
*Remote<`CR`><`LF`>*\


**NOTE**: To exit Remote Command mode send “!,0<`CR`>”.

Expected response:   *%RMT_CMD_OFF%*

##Master Control with an Android Device
Using an Android device involves more effort than using two RN4871 modules. Since the Android device does not support the same exact commands as an RN4871 module these commands must be written to the BLE characteristics using an Android App such as Smart Discover. All data is passed as hex data so any ASCII commands must be converted first. The step by step procedure to configure is provided below.

1. Create a secure, bonded connection between your Android device and the RN4871 Remote module. This can be done using the Microchip Bluetooth Data (MBD) App on your Android device. Launch the App and select the Smart Discovery tool. The RN4871 module should appear advertising as 'Remote'. Click on it to establish connection.
![MBD App screen 1]((https://github.com/MchpBTApps/RN4870_Remote_Configuration/blob/main/Figures/MBDApp1.png)
