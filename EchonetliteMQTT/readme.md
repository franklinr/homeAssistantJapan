# Connecting Panasonic distribution board to Home Assistant

Many smart devices in Japan use echonetlite to send their information to devices that aggregate that information.  To import this data into HA, we setup a MQTT server that will collect echonetlite data and publish it.  Then HA can read this server and import that data into HA.  Here I will show to connect a channel from my air conditioner to home assistant, so that I can monitor when the air condition is on.

![Dashboard](https://github.com/franklinr/homeAssistantJapan/blob/57bfbd12f37f9a588ebf6e8ff24417e4a017e4e9/EchonetliteMQTT/distboard.JPG)

Panasonic Residential distribution board 
住宅分電盤専用 
Compatible with Echonet Lite for Smart Cosmo
[スマートコスモ用エコーネットライト対応](https://www2.panasonic.biz/jp/densetsu/denro/compact21/smartcosmo/hems/)

1) Go to Home Assistant AddOn page in settings and add [Mosquito broker](https://github.com/home-assistant/addons/tree/master/mosquitto).  Install it and start it up the broker.  This is a server that can recieve and share messages from mqtt transmitting devices.

2) Create ECHONET Lite to MQTT bridge, which will find Echonetlite devices and transmit their data to your MQTT broker.  I am running in synology, so I used docker to setup the [echonetlite2mqtt](https://github.com/banban525/echonetlite2mqtt) bridge.

  a) Create a file [config.json](https://github.com/franklinr/homeAssistantJapan/blob/09721c25c3e3eb5fa90b16e9e3e23c69b0f28354/EchonetliteMQTT/config.json) with information for logging into your home assistant system.

  b) Find and download banban525 echonetlite2mqtt in the registry.
  ![registry](https://github.com/franklinr/homeAssistantJapan/blob/09721c25c3e3eb5fa90b16e9e3e23c69b0f28354/EchonetliteMQTT/registry.png)
  
  c) In the image tab, launch the banban525 echonetlite2mqtt image. 
  
  d) Click on advance settings.  In Volume, add the config.json file that you made earlier and set it to be at app/config/config.json.  Network should be bridge.
  
  e) In the environment variables, add MQTT_OPTION_FILE variable which is set to app/config/config.json.
  
  f) Add an MQTT_BROKER variable which has a link to your mqtt server like mqtt://192.168.X.X:1883.  This transmits the data in this docker mqtt server to HA mosquito addon.
  ![variables](https://github.com/franklinr/homeAssistantJapan/blob/09721c25c3e3eb5fa90b16e9e3e23c69b0f28354/EchonetliteMQTT/env.png)
  
  g) Launch your server. I was not able to access it directly using http://localhost:3000.
  
3) Next you need to add code to the configuration.yaml to tell them which channels in the distribution board shoul be brought into HA.  For example, my 2nd floor air conditioner is on channel 11, so I import that as a sensor using this code.

https://github.com/franklinr/homeAssistantJapan/blob/main/EchonetliteMQTT/configuration.yaml#L1-L9

4) To see when the air conditioner is on, just use a statistic card with stat_type change and use the new channels that you imported.
```
chart_type: line
period: 5minute
days_to_show: 2
type: statistics-graph
entities:
  - sensor.energy_channel_14total
  - sensor.energy_channel_11total
  - sensor.energy_channel_21total
stat_types:
  - change
```

For more information about how to use mqtt with a wide range of devices, see this page [Home Assistant to Echonetlight using mqtt] (https://qiita.com/banban525/items/520caead4f5f8dbb9ced).


