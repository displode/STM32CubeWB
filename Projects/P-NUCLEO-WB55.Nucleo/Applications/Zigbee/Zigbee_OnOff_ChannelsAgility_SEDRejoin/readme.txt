/**
  @page Zigbee_OnOff_ChannelsAgility_SEDRejoin application
  
  @verbatim
  ******************************************************************************
  * @file    Zigbee/Zigbee_OnOff_ChannelsAgility_SEDRejoin/readme.txt 
  * @author  MCD Application Team
  * @brief   Description of the Zigbee Rejoin procedure in a centralized network. 
  ******************************************************************************
  *
  * Copyright (c) 2021 STMicroelectronics. All rights reserved.
  *
  * This software component is licensed by ST under Ultimate Liberty license 
  * SLA0044, the "License"; You may not use this file except in compliance with 
  * the License. You may obtain a copy of the License at:
  *                               www.st.com/SLA0044
  *
  ******************************************************************************
  @endverbatim

@par Application Description 

How to use the OnOff cluster on a device acting as a Client with EndDevice role within a Centralized Zigbee network.

The purpose of this application is mainy to make use of the "non automatic rejoin" (optional) without having conflict with the "automatic rejoin".
Application create a Zigbee centralized network, and 
how to communicate from one node to another one using the OnOff cluster. Once the Zigbee mesh 
network is created, the user can send requests from the client to the server through the push button 
in order to make the LED toggling.
 Rejoin automatic is set with a values of 1 (=30 seconds) while the "non automatic rejoin" is triggered after 20 seconds, in case of "rejoin automatic" failure.
 Conclusion is that way of doing a "non automatic rejoin" is not entering into conflict with the "automatic rejoin" nor the "OnOff toggling 4 seconds application".

A number of experiments are described in this readme, with multiple ZC (ZigbeeCoordinator: ZC1, ZC2) and ZR (ZigbeeRouter: ZR1,ZR2):

- ZCx STM32WB55xx board loaded with: 
    - wireless coprocessor : stm32wb5x_Zigbee_FFD_fw.bin
    - application : Zigbee_OnOff_ChannelsAgility_ZC
    
- ZRx or more STM32WB55xx board loaded with: 
    - wireless coprocessor : stm32wb5x_Zigbee_FFD_fw.bin
    - application : Zigbee_OnOff_ChannelsAgility_ZR
    
- 1 STM32WB55xx board loaded with: 
    - wireless coprocessor : stm32wb5x_Zigbee_FFD_fw.bin
    - application : Zigbee_OnOff_ChannelsAgility_SEDRejoin
    
TABLE_OF_CONTENT_DEMO_DESCRIPTION

SUMMARY ZR[1-2]+ZC[1-2]+SED[1-2], at the end, network1 SED1--ZR1--ZC1 exist (within a distance of 5 meters), and network2 SED2--ZR2--ZC2 exist (likely on a different channel)

TABLE_OF_CONTENT_DEMO_DESCRIPTION_END
 
    
Setup_1.1 with SED1,SED2,ZR1,ZR2,ZC1,ZC2 : Read Setup_4 till Setup_4_END before proceeding, since sequencing of PowerOn is important 
and since a number of minutes is necessary to perform this setup.
Setup_1.2 Part SED1-ZR1-ZC1 CHANNEL 15 (Hypothesis that channel 15, first channel listed in increasing order, is "100% free")

              Device ZR1                                   Device ZC1
Device        
             ---------                                      ---------
             |       |       ZbZclOnOffClientToggleReq      |       |
SED1  PushB=>|Client | -----------------------------------> |Server | =>LED_RED and LED_GREEN are toggling (AND LED_RED TOGGLE with ZR)
------       |       |                                      |       |
|    |-------|Blue   |                                      |Blue   |
|    |       ---------                                      ---------
------

Setup_1.3 WAIT more then 3 minutes, then PowerOn ZC2 AND ZR2 and SED2 (SED2 need to be power on last)
Setup_1.4 Part SED2-ZR2-ZC2 CHANNEL 20 (Hypothesis: Channel20 also free)

              Device ZR2                                   Device ZC2
Device        
             ---------                                      ---------
             |       |       ZbZclOnOffClientToggleReq      |       |
SED2  PushB=>|Client | -----------------------------------> |Server | =>LED_RED and LED_GREEN are toggling (AND LED_RED TOGGLE with ZR)
------       |       |                                      |       |
|    |-------|Blue   |                                      |Blue   |
|    |       ---------                                      ---------
------

Setup_1_SUMMARY ZR[1-2]+ZC[1-2]+SED[1-2], at the end, network1 SED1--ZR1--ZC1 exist (within a distance of 5 meters), and network2 SED2--ZR2--ZC2 exist (likely on a different channel)
NOTE on Blue Led on SEDx : Blue LED is ON till a callback verifying that ACK fails. Saying differently: after the first fail of getting an ack, the the SED blue LED is turned OFF.
 This can be easily observed if the SED is moved 10 meters away from the ZR-ZC.  NOTE: tx_power is set to -20db in this application, hence radius limitation is about 5 meters. 
NOTE for Setup_4: Assumption that the CHANNELS 15,20,23 are free, and that ZCy is starting before Zxxy, and all Zxxy-ZCy) are joining within a timeframe of less then 180 seconds
Setup_1_END  


  
To setup the application :

  a)  Open the project, build it and load your generated application on your STM32WB devices.
  
 Additional detailed comments on the application :

  a)  Start coordinator first. So in this demo application it is
      the device running Zigbee_OnOff_ChannelsAgility_ZCRejoin application (Device ZCx in the above diagram). 
      Wait for the Blue LED (LED1) ON. 
      Second board: Zigbee Router. This board is configured as Zigbee router and will attached to the network created 
      by the coordinator. Do the same for the other boards if applicable.
            
  b)  SED stage, the Zigbee network is automatically created and NO activity will be observed on any SED LED on all SED devices.
      it is possible to detect that the OnOff Toggle Cluster commands from the client SED to the server in unicast mode is succeeding. 
      You must see the RED LED (LED3) toggling on the CONNECTED ZC server side (Using same CHANNEL). In case of multiple ZC, the 4 seconds interval 
      allow to detect which SED is connected to which ZC.
      
  c)  ZR stage, the Zigbee network is automatically created when the BLUE LED (LED1) is ON on all ZR devices.
      it is now possible to send OnOff Cluster commands from the client ZR to the server in unicast mode 
      by pressing on the SW1 push button. 
      You must see the RED LED (LED3) toggling on the CONNECTED server side (Using same CHANNEL).

 Note: when LED1, LED2 and LED3 are toggling serially with an identical tempo it is indicating an error has occurred on application.

@par Keywords

Zigbee
 
@par Hardware and Software environment

  - This example runs on STM32WB55xx devices.
  
  - This example has been tested with an STMicroelectronics STM32WB55RG_Nucleo 
    board and can be easily tailored to any other supported device 
    and development board.
    
  - On STM32WB55RG_Nucleo, the jumpers must be configured as described
    in this section. Starting from the top left position up to the bottom 
    right position, the jumpers on the Board must be set as follows: 

     CN11:    GND         [OFF]
     JP4:     VDDRF       [ON]
     JP6:     VC0         [ON]
     JP2:     +3V3        [ON] 
     JP1:     USB_STL     [ON]   All others [OFF]
     CN12:    GND         [OFF]
     CN7:     <All>       [OFF]
     JP3:     VDD_MCU     [ON]
     JP5:     GND         [OFF]  All others [ON]
     CN10:    <All>       [OFF]


@par How to use it ? 

=> Loading of the stm32wb5x_Zigbee_FFD_fw.bin binary

  This application requests having the stm32wb5x_Zigbee_FFD_fw.bin binary flashed on the Wireless Coprocessor.
  If it is not the case, you need to use STM32CubeProgrammer to load the appropriate binary.
  All available binaries are located under /Projects/STM32_Copro_Wireless_Binaries directory.
  Refer to UM2237 to learn how to use/install STM32CubeProgrammer.
  Refer to /Projects/STM32_Copro_Wireless_Binaries/ReleaseNote.html for the detailed procedure to change the
  Wireless Coprocessor binary. 

=> Getting traces
  To get the traces you need to connect your Board to the Hyperterminal (through the STLink Virtual COM Port).
  The UART must be configured as follows:

    - BaudRate = 115200 baud  
    - Word Length = 8 Bits 
    - Stop Bit = 1 bit
    - Parity = none
    - Flow control = none

=> Running the application

  Refer to the Application description at the beginning of this readme.txt

 * <h3><center>&copy; COPYRIGHT STMicroelectronics</center></h3>

