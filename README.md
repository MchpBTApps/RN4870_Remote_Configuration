# RN4870 Remote Configuration feature
This note describes the procedure required to demonstrate the RN4871 remote command feature. This feature allows the control of an RN4871 module without the use of a host microcontroller remotely from either another RN4871 module or an Android app.

<!--- This document is written by Raghu (Microchip Tech) to explain the use to the Remote configuration feature in the RN4871 modules.) -->

The remote command feature works by using a special BLE GATT service that emulates a wired serial connection called transparent UART mode. This combined with a unique command set allows remote functionality of the module.

The remote command feature can be controlled by either another RN4871 module, a competitors BLE module or an Android app.

For the first demonstration two RN4871 modules will be used. One will be called the Central Module the other the Remote Module. The Central Module is connected to the PC and is in control of the Remote Module. The Remote Module is connected to the PC for configuration and status feedback only.

We will configure the Remote Module first, so when we get to the connection sequence on the Central module, it can connect properly

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

The above steps complete the configuration for the RN4871 Remote Module. Next the RN4871 Central Module is configured as outlined below.

1. Enter command mode by sending $$$
    - TX: *$$$*
    - RX: *CMD>*
2. Set to factory defaults by sending “SF,1 <`CR`>”
    - TX: *SF,1<`CR`>*
    - RX: *Reboot after Factory Reset<`CR`><`LF`>%REBOOT%*
3. Enter command mode by sending $$$
    - TX: *$$$*
    - RX: *CMD>*
4. Set name of module to “Remote” by sending “SN Central<`CR`>”
    - TX: *SN Central<`CR`>*
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

## Central Control with an Android Device

Using an Android device involves more effort than using two RN4871 modules. Since the Android device does not support the same exact commands as an RN4871 module these commands must be written to the BLE characteristics using an Android App such as Smart Discover. All data is passed as hex data so any ASCII commands must be converted first. The step by step procedure to configure is provided below.

1. Create a secure, bonded connection between your Android device and the RN4871 Remote module. This can be done using the Microchip Bluetooth Data (MBD) App on your Android device. Launch the App and select the Smart Discovery tool. The RN4871 module should appear advertising as 'Remote'. Click on it to establish connection.
![MBD App screen 1](https://github.com/MchpBTApps/RN4870_Remote_Configuration/blob/main/Figures/MBDApp1.png)

2. The connection status intially should show up as 'Disconnected'. Click on the top-right menu (3 vertical dots) and the option 'Bond' should show up. Click on 'Bond'. The status should change to 'Disconnected - Bonded'.

3. Click on the 'Connect' button on the top right.
![MBD App screen 2](https://github.com/MchpBTApps/RN4870_Remote_Configuration/blob/main/Figures/MBDApp2.png)

4. Once the connection is established you should see the two services shown below – Device Information and Microchip Data Service, as shown below.
![MBD App screen 4](https://github.com/MchpBTApps/RN4870_Remote_Configuration/blob/main/Figures/MBDApp3.png)

5. Once the connection is established you should see the two services shown below – Device Information and Microchip Data Service, as shown below.
![MBD App screen 5](https://github.com/MchpBTApps/RN4870_Remote_Configuration/blob/main/Figures/MBDApp4.png)
You should also see the connection being established on the RN4871 Remote module side with a PC serial terminal with a message similar to 
*%CONNECT,1,5C434D58D439%%CONN_PARAM,0006,0000,01F4%%SECURED%%CONN_PARAM,0027,0000,01F4%%BONDED%%CONN_PARAM,0006,0000,01F4%%CONN_PARAM,0027,0000,01F4%%DISCONNECT%%CONNECT,1,79E6F8B64221%%SECURED%*

6. Select the “Microchip Data Service” and you will see this service has three Characteristics. The Transparent UART Service is the primary service with a UUID set to:  
49535343-FE7D-4AE5-8FA9-9FAFD205E455.
- The Transparent UART Service contains the following characteristics:
  - Transparent UART TX: 
    - Attributes: Write, Write without response, Notify and Indicate.
    - 49535343-1E4D-4BD9-BA61-23C647249616
    - This characteristic is used for transmitting data to the RN4871 Remote module.
 
  - Transparent UART RX: 
    - Attributes: Write and Write without response.
    - 49535343-8841-43F4-A8D4-ECBE34729BB3 
    - This characteristic is used for receiving data from the RN4871 Remote module. 

  - Transparent UART Control: 
    - Attributes: Write and Notify.
    - 49535343-4C8A-39B3-2F49-511CFF073B7E
    - This characteristic is used for control data with the RN4871 Remote module

![MBD App screen 6](https://github.com/MchpBTApps/RN4870_Remote_Configuration/blob/main/Figures/MBDApp5.png)

7. To get into the Remote Command mode, we need to send the PIN code to the RN4871 Remote module. Since the Android device does not support the same exact methods, we must write to the associated characteristic. Sending an ASCII “Y” first and then the PIN code will accomplish this. Converting the ASCII “Y1234” into a hex representation of 5931323334 is required. Select the third characteristic ending in B7E. Now enter 5931323334 into the box labelled “Write” and click the “Write” button.

![MBD App screen 7](https://github.com/MchpBTApps/RN4870_Remote_Configuration/blob/main/Figures/MBDApp6.png)


You should now see the message: *%RMT_CMD_ON%*

We are now in Remote Command mode (to exit Remote Command mode write hex 46).

### Example 1:

In this example we will send a command to the RN4871 Remote module asking it for its firmware version. To do this we will write a hex command to the first characteristic 49535343-1E4D-4BD9-BA61-23C647249616.

First, we need to enable notifications to see the returned status coming from the RN4871 Remote module. In the box labeled “Read”, there is an enable switch. Move this to the “On” position.
 
Now we can send the command to the RN4871 Remote module. In module to module commands we would send the ASCII command V<`CR`> but here we must convert the ASCII commands into hex. The hex representation of ASCII “V” is 0x56 and <`CR`> (Carriage Return) is 0x0D. The hex numbers to be sent would be 560D. Now in the box labelled “Write” enter 560D then click the “Write” button. You should see a response in the notification window above. Remember this is in hex notation so if you convert this into ASCII it should resemble:

*RN4871 V1.18.3 4/14/2016 (c)Microchip Technology Inc<`CR`><`LF`>*

*RMT>*

Converting ASCII into hex the content is:

*RN4871*		52 4E 34 38 37 31

*V1.18.3*		56 31 2E 31 38 2E 33

*4/14/2016*		34 2F 31 34 2F 32 30 31 36

*(c)Microchip Technology Inc*	28 63 29 4D 69 63 72 6F 63 68 69 70 20 54 65 63 68 6E 6F 6C 6F 67 79 20 49 6E 63

*<`CR`><`LF`>*		0D 0A

*RMT>*			52 4D 54 3E 20

### Example 2:
In this example we will ask the RN4871 Remote module to “Dump” it’s information. The command will reply with the following information about the RN4871 module:

*Bluetooth Address* 3481F407DB41 *<`CR`><`LF`>*

*Module Name* Remote *<`CR`><`LF`>*

*Connection Status* no *<`CR`><`LF`>*

*Security Status* 2 *<`CR`><`LF`>*

*Features* 0000 *<`CR`><`LF`>*

*Services* C0 *<`CR`><`LF`>*


(For more details please refer to the RN4870/71 Bluetooth Low Energy Module User’s Guide)

The hex representation of ASCII “D” is 0x44 and *<`CR`>* (Carriage Return) is 0x0D. The hex numbers to be sent would be 440D. In the box labelled “Write” enter *440D* then click the “Write” button. You should see a response in the notification window above. Remember this is in hex notation so if you convert this into ASCII it should resemble:

BTA=3481F407DB41 *<`CR`><`LF`>*

Name=Remote *<`CR`><`LF`>*

Connected=no *<`CR`><`LF`>*

Authen=2 *<`CR`><`LF`>*

Features=0000 *<`CR`><`LF`>*

Services=C0 *<`CR`><`LF`>*



Converting the ASCII info into hex, the info shows up as :

BTA=3481F407DB41 *<`CR`><`LF`>*

	42 54 41 3D 33 34 38 31 46 34 30 37 44 42 34 31 0D 0A

Name=Remote *<`CR`><`LF`>*

	4E 61 6D 65 3D 52 65 6D 6F 74 65 0D 0A

Connected=no *<`CR`><`LF`>*

	43 6F 6E 6E 65 63 74 65 64 3D 6E 6F 0D 0A

Authen=2 *<`CR`><`LF`>*

	41 75 74 68 65 6E 3D 32 0D 0A

Features=0000 *<`CR`><`LF`>*

	46 65 61 74 75 72 65 73 3D 30 30 30 30 0D 0A

Services=C0 *<`CR`><`LF`>*

	53 65 72 76 69 63 65 73 3D 43 30 0D 0A

RMT>

	52 4D 54 3E 20
			

### Example 3:

In this example we will control an LED on the RN4871 PICtail Plus board. To prepare for this, add a jumper wire between the LED TEST header LED2 and APP Default header J14 pin 6/P1_2.

*Turn LED On*

The ASCII command for this is |O,08,00<CR>
 
The HEX command for this is 7C4F2C30382C30300D



*Turn LED Off*

The ASCII command for this is |O,08,08<CR>
 
The HEX command for this is 7C4F2C30382C30380D


On the Smart Discovery app, write these values to the characteristic 49535343-1E4D-4BD9-BA61-23C647249616 to control the LED status.

### Ending the Remote Command Session

Now that we have used the Remote Command mode to control the RN4871 Remote module, there is also a need to end the Remote Command session.
 
The ASCII command for this is F *<`CR`>*

The HEX command for this is 460D

On the Smart Discovery app write these values to the characteristic 
49535343-4C8A-39B3-2F49-511CFF073B7E to exit Remote Command mode.

You should now see the message:		*%RMT_CMD_OFF%*
