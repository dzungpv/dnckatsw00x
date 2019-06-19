## Ligh Switch base on Samplight Z-Stack Home 1.2.2a for CC2530, 2538 SOC

I use this board as light switch/high load switch, for a long distant normal one could not reach and I can choose any high current relay and contactor for very high load.
Support up to 4 relay and button control.

The module include an external antena can reach 200m distant: *Z-0001 by WEBEE*.

#### Where to buy:

- Include 2 module, kit and programing/debug tool: [Kit](https://www.aliexpress.com/store/product/Network-of-things-things-ZigBee-wireless-module-CC2530-development-kit-learning-board-intelligent-hardware-control/2980023_32806496292.html)
- Only the module: [Module](https://www.aliexpress.com/item/CC2530-Zigbee-UART-Wireless-Core-Board-Development-Board-CC2530F256-Serial-Port-Wireless-Module-24MHz/32767470382.html)

![My product](https://raw.githubusercontent.com/dzungpv/dnckatsw001/master/dnckatsw001.jpg)

#### Setup the relay/Contactor, and button:

- Connect relay 1 control pin with P1.0, relay 2 with P1.1, relay 3 with P1.2, relay 4 with P1.3, 
- Connect your button 1 with P0.0, button 2 with P0.1, button 3 with P0.4, button 4 with P0.5

#### Prebuild hex:

Version with 1 relay and button ready to flash with your tool download it here: [dnckatsw00x.hex](https://raw.githubusercontent.com/dzungpv/dnckatsw00x/master/dnckatsw00x.hex) and use channel 15

#### Support gateway/hub/software:

Please check: 

[Zigbee2Mqtt](https://github.com/Koenkk/zigbee2mqtt)

[Home Assistant](https://www.home-assistant.io/)


### Build it from scratch:

#### Step 1:
Download Z-Stack Home 1.2.2a it is free but require to register [Z-Stack Home](http://www.ti.com/tool/Z-STACK-ARCHIVE)

#### Step 2:
Edit file C:\Texas Instruments\Z-Stack Home 1.2.2a.44539\Projects\zstack\HomeAutomation\SampleLight\Source\zcl_samplelight_data.c with those infor:
```
const uint8 zclSampleLight_ManufacturerName[] = { 6, 'D','N','C','K','A','T' };
const uint8 zclSampleLight_ModelId[] = { 11, 'D','N','C','K','A','T','_','S','0','0','X' };// X is number from 1 to 4 defend on your usage
const uint8 zclSampleLight_DateCode[] = { 16, '2','0','1','8','0','6','2','3',' ',' ',' ',' ',' ',' ',' ',' ' };
```

#### Step 3:
Add more code and definition below
Add report state code to zclSampleLight_LcdDisplayUpdate:
Report for 1 relay verions
```
  //report state
  zclSampleLightSeqNumState++;
  zclReportCmd_t rptcmd;
  rptcmd.numAttr = 1;
  rptcmd.attrList[0].attrID = ATTRID_ON_OFF;
  rptcmd.attrList[0].dataType = ZCL_DATATYPE_BOOLEAN;
  rptcmd.attrList[0].attrData = (void *)(&zclSampleLight_OnOff);

  // Set destination address to indirect
  zclSampleLight_DstAddr.addrMode = (afAddrMode_t)Addr16Bit;
  zclSampleLight_DstAddr.addr.shortAddr = 0;
  zclSampleLight_DstAddr.endPoint=1;
  zcl_SendReportCmd(SAMPLELIGHT_ENDPOINT,&zclSampleLight_DstAddr, ZCL_CLUSTER_ID_GEN_ON_OFF, &rptcmd, ZCL_FRAME_SERVER_CLIENT_DIR, true, zclSampleLightSeqNumState );
  ```
 For 2 relay verions just add more on/off variable zclSampleLight_OnOff1, register 1 more endpoint: SAMPLELIGHT_ENDPOINT+ 1 in zclSampleLight_Init()
 
 Add more Endpoint SimpleDescription, you can add more with 3 and 4 button version 
```
SimpleDescriptionFormat_t zclSampleLight_SimpleDesc[2] =
{
{
  SAMPLELIGHT_ENDPOINT,                  //  int Endpoint;
  ZCL_HA_PROFILE_ID,                     //  uint16 AppProfId;
#ifdef ZCL_LEVEL_CTRL
  ZCL_HA_DEVICEID_DIMMABLE_LIGHT,        //  uint16 AppDeviceId;
#else
  ZCL_HA_DEVICEID_ON_OFF_LIGHT,          //  uint16 AppDeviceId;
#endif
  SAMPLELIGHT_DEVICE_VERSION,            //  int   AppDevVer:4;
  SAMPLELIGHT_FLAGS,                     //  int   AppFlags:4;
  ZCLSAMPLELIGHT_MAX_INCLUSTERS,         //  byte  AppNumInClusters;
  (cId_t *)zclSampleLight_InClusterList, //  byte *pAppInClusterList;
  ZCLSAMPLELIGHT_MAX_OUTCLUSTERS,        //  byte  AppNumInClusters;
  (cId_t *)zclSampleLight_OutClusterList //  byte *pAppInClusterList;
  },
  {
  SAMPLELIGHT_ENDPOINT,                  //  int Endpoint;
  ZCL_HA_PROFILE_ID,                     //  uint16 AppProfId;
#ifdef ZCL_LEVEL_CTRL
  ZCL_HA_DEVICEID_DIMMABLE_LIGHT,        //  uint16 AppDeviceId;
#else
  ZCL_HA_DEVICEID_ON_OFF_LIGHT,          //  uint16 AppDeviceId;
#endif
  SAMPLELIGHT_DEVICE_VERSION,            //  int   AppDevVer:4;
  SAMPLELIGHT_FLAGS,                     //  int   AppFlags:4;
  ZCLSAMPLELIGHT_MAX_INCLUSTERS,         //  byte  AppNumInClusters;
  (cId_t *)zclSampleLight_InClusterList, //  byte *pAppInClusterList;
  ZCLSAMPLELIGHT_MAX_OUTCLUSTERS,        //  byte  AppNumInClusters;
  (cId_t *)zclSampleLight_OutClusterList //  byte *pAppInClusterList;
  },
  {
  SAMPLELIGHT_ENDPOINT+1,                  //  int Endpoint;
  ZCL_HA_PROFILE_ID,                     //  uint16 AppProfId;
#ifdef ZCL_LEVEL_CTRL
  ZCL_HA_DEVICEID_DIMMABLE_LIGHT,        //  uint16 AppDeviceId;
#else
  ZCL_HA_DEVICEID_ON_OFF_LIGHT,          //  uint16 AppDeviceId;
#endif
  SAMPLELIGHT_DEVICE_VERSION,            //  int   AppDevVer:4;
  SAMPLELIGHT_FLAGS,                     //  int   AppFlags:4;
  ZCLSAMPLELIGHT_MAX_INCLUSTERS,         //  byte  AppNumInClusters;
  (cId_t *)zclSampleLight_InClusterList, //  byte *pAppInClusterList;
  ZCLSAMPLELIGHT_MAX_OUTCLUSTERS,        //  byte  AppNumInClusters;
  (cId_t *)zclSampleLight_OutClusterList //  byte *pAppInClusterList;
  }
};
```

Add to zclSampleLight_Init()
```
  // This app is part of the Home Automation Profile
  zclHA_Init( &zclSampleLight_SimpleDesc[0] ); //button 1
  zclHA_Init( &zclSampleLight_SimpleDesc[1] ); //button 2

  // Register the ZCL General Cluster Library callback functions
  zclGeneral_RegisterCmdCallbacks( SAMPLELIGHT_ENDPOINT, &zclSampleLight_CmdCallbacks );//button 1
  zclGeneral_RegisterCmdCallbacks( SAMPLELIGHT_ENDPOINT+1, &zclSampleLight_CmdCallbacks );//button 2

  // Register the application's attribute list
  zcl_registerAttrList( SAMPLELIGHT_ENDPOINT, zclSampleLight_NumAttributes, zclSampleLight_Attrs );//button 1
  zcl_registerAttrList( SAMPLELIGHT_ENDPOINT+1, zclSampleLight_NumAttributes, zclSampleLight_Attrs );//button 2

  // Register the Application to receive the unprocessed Foundation command/response messages
  zcl_registerForMsg( zclSampleLight_TaskID );

#ifdef ZCL_DISCOVER
  // Register the application's command list
  zcl_registerCmdList( SAMPLELIGHT_ENDPOINT, zclCmdsArraySize, zclSampleLight_Cmds );//button 1
  zcl_registerCmdList( SAMPLELIGHT_ENDPOINT+1, zclCmdsArraySize, zclSampleLight_Cmds );//button 1
#endif
```
Add report code
```
  //report state for button 1
  zclSampleLightSeqNumState++;
  zclReportCmd_t rptcmd;
  rptcmd.numAttr = 1;
  rptcmd.attrList[0].attrID = ATTRID_ON_OFF;
  rptcmd.attrList[0].dataType = ZCL_DATATYPE_BOOLEAN;
  rptcmd.attrList[0].attrData = (void *)(&zclSampleLight_OnOff);

  // Set destination address to indirect
  zclSampleLight_DstAddr.addrMode = (afAddrMode_t)Addr16Bit;
  zclSampleLight_DstAddr.addr.shortAddr = 0;
  zclSampleLight_DstAddr.endPoint=1;
  zcl_SendReportCmd(SAMPLELIGHT_ENDPOINT,&zclSampleLight_DstAddr, ZCL_CLUSTER_ID_GEN_ON_OFF, &rptcmd, ZCL_FRAME_SERVER_CLIENT_DIR, true, zclSampleLightSeqNumState );
  //report state for button 2
  zclSampleLightSeqNumState++;
  zclReportCmd_t rptcmd;
  rptcmd.numAttr = 1;
  rptcmd.attrList[0].attrID = ATTRID_ON_OFF;
  rptcmd.attrList[0].dataType = ZCL_DATATYPE_BOOLEAN;
  rptcmd.attrList[0].attrData = (void *)(&zclSampleLight_OnOff1);

  // Set destination address to indirect
  zclSampleLight_DstAddr.addrMode = (afAddrMode_t)Addr16Bit;
  zclSampleLight_DstAddr.addr.shortAddr = 0;
  zclSampleLight_DstAddr.endPoint=1;
  zcl_SendReportCmd(SAMPLELIGHT_ENDPOINT+1,&zclSampleLight_DstAddr, ZCL_CLUSTER_ID_GEN_ON_OFF, &rptcmd, ZCL_FRAME_SERVER_CLIENT_DIR, true, zclSampleLightSeqNumState );
  ``` 
  Handle incomming command in zclSampleLight_OnOffCB:
  
  ```
  static void zclSampleLight_OnOffCB( uint8 cmd )
{
  afIncomingMSGPacket_t *pPtr = zcl_getRawAFMsg();
  if (pPtr->endPoint == SAMPLELIGHT_ENDPOINT) //button 1
  {
  ...
  }
  else if (pPtr->endPoint == SAMPLELIGHT_ENDPOINT+1) //button 2
  {
  ...
  }
  else if (pPtr->endPoint == SAMPLELIGHT_ENDPOINT+2) //button 3
  {
  ...
  }
  else if (pPtr->endPoint == SAMPLELIGHT_ENDPOINT+3) //button 4
  {
  ...
  }
}
  ```
  
You can see 2 project i learn from here https://github.com/presslab-us/ZigBee_WDHA-12 and https://github.com/presslab-us/ZigBee_OpenEVSE
#### Step 4:
Disable ZCL_EZMODE and HOLD_AUTO_START build tag by add 'x' before each, and finally build and download to your module

### Configuration for Home Assistant

#### 1 button
```
  - platform: "mqtt"
    name: DNCKAT_S001
    state_topic: "zigbee2mqtt/FRIENDLY_NAME"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.state }}"
    command_topic: "zigbee2mqtt/FRIENDLY_NAME/set"
```
#### 2 button
```
# zigbee 2G L      
  - platform: "mqtt"
    name: "DNCKAT_S002 Left"
    state_topic: "zigbee2mqtt/FRIENDLY_NAME"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.state_left }}"
    command_topic: "zigbee2mqtt/FRIENDLY_NAME/left/set"
# zigbee 2G R   
  - platform: "mqtt"
    name: "DNCKAT_S002 Right"
    state_topic: "zigbee2mqtt/FRIENDLY_NAME"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.state_right }}"
    command_topic: "zigbee2mqtt/FRIENDLY_NAME/right/set"
```
#### 3 button
```
# zigbee 3G L      
  - platform: "mqtt"
    name: "DNCKAT_S003 Left"
    state_topic: "zigbee2mqtt/FRIENDLY_NAME"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.state_left }}";
    command_topic: "zigbee2mqtt/FRIENDLY_NAME/left/set"
 # zigbee 3G Center      
  - platform: "mqtt"
    name: "DNCKAT_S003 Center"
    state_topic: "zigbee2mqtt/FRIENDLY_NAME"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.state_center }}";
    command_topic: "zigbee2mqtt/FRIENDLY_NAME/center/set"   
# zigbee 3G R   
  - platform: "mqtt"
    name: "DNCKAT_S003 Right"
    state_topic: "zigbee2mqtt/FRIENDLY_NAME"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.state_right }}";
    command_topic: "zigbee2mqtt/FRIENDLY_NAME/right/set"
```
#### 4 button
```
 #zigbee 4G TL      
  - platform: "mqtt"
    name: "DNCKAT_S004 Top Left"
    state_topic: "zigbee2mqtt/FRIENDLY_NAME"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.state_top_left }}"
    command_topic: "zigbee2mqtt/FRIENDLY_NAME/top_left/set"
# zigbee 4G TR   
  - platform: "mqtt"
    name: "DNCKAT_S004 Top Right"
    state_topic: "zigbee2mqtt/FRIENDLY_NAME"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.state_top_right }}";
    command_topic: "zigbee2mqtt/FRIENDLY_NAME/top_right/set"  
 # zigbee 4G BL      
  - platform: "mqtt"
    name: "DNCKAT_S004 Bottom Left"
    state_topic: "zigbee2mqtt/FRIENDLY_NAME"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.state_bot_left }}";
    command_topic: "zigbee2mqtt/FRIENDLY_NAME/bot_left/set"
# zigbee 4G BR   
  - platform: "mqtt"
    name: "DNCKAT_S004 Bottom Right"
    state_topic: "zigbee2mqtt/FRIENDLY_NAME"
    availability_topic: "zigbee2mqtt/bridge/state"
    payload_off: "OFF"
    payload_on: "ON"
    value_template: "{{ value_json.state_bot_right }}";
    command_topic: "zigbee2mqtt/FRIENDLY_NAME/bot_right/set" 
```
