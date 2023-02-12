# Amcrest AD410 doorbell with two way audio in home assistant synology NAS.

![Dashboard](https://github.com/franklinr/amcrestDoorbellHomeAssistant/blob/204fd846c63da347bc2cde105e8add385d705374/dash.png)

Here is a dashboard that can be used to interact with the Amcrest AD410 doorbell.  Pressing the listen button turns on the doorbell's audio.  Pressing the speak button allows you to talk to the person at the doorbell.  The video feed is shown on the right.

I am using home assistant in a virtual machine on a synology NAS drive. My setup is based on the information the video by Roger's tech talk https://www.youtube.com/watch?v=ZN9HjAJW9Xc.  I have simplified things and tried to use existing features of my synology machine (frigate, mkcert, and audio alerts have been left out).

1. Install home assistant.  I followed this page.
https://community.home-assistant.io/t/installation-on-synology-virtual-machine-managager/281608

2. Install go2rtc: https://github.com/AlexxIT/go2rtc

3. Setup your [go2rtc.yaml](https://github.com/franklinr/amcrestdoorbell/blob/23e9673e2d077ab0fac55232e5f3c4a977b9153b/config/go2rtc.yaml) in your config folder in home assistant.  Put this in the file to test it and add the password and address for your AD410 doorbell.  The rtsp line will show the video and the ffmpeg line will turn on the sound by default.

https://github.com/franklinr/amcrestDoorbellHomeAssistant/blob/0722ded44209651057b28032f015173cbf0a4bb9/config/go2rtc.yaml#L1-L11

You should see the amcrestdoorbellnoback in go2rtc in the sidepanel.  Click on stream should let you see your camera stream.  

4. We need to setup https access so that your browser will allow the microphone to be used.  Replace MYMACHINE with the name of your machine below.
    - Go to control panel/security/certificates and add a certificate for home.MYMACHINE.synology.me and gortc.MYMACHINE.synology.me
    - Go to application portal/reverse proxy
        - create a homeassistant reverse proxy from source https://home.MYMACHINE.synology.me:443 to your http home assistant url with port 8123
        - create a gortc reverse proxy from source https://gortc.MYMACHINE.synology.me:443 to your http home assistant url with port 1984 (enable access control all)
    - If you go to https://home.MYMACHINE.synology.me, you should see your home assistant lovelace UI. If you go to https://gortc.MYMACHINE.synology.me you should see your gortc page.   

5. Test whether you can directly access the streams
  - This link should give you the sound from the doorbell (click on the unmute button to hear the sound).
    https://gortc.MYMACHINE.synology.me/stream.html?src=soundonly
  - This link should give you the video with no sound
    https://gortc.MYMACHINE.synology.me/webrtc.html?src=amcrestdoorbellnoback
  - Test that speaking to your computer can be heard through the doorbell speaker.  You may need to allow the microphone to be used in chrome.  This will only work if https has been setup properly.
    https://gortc.MYMACHINE.synology.me/webrtc.html?src=amcrestdoorbellback

6. Create a www folder in the config folder.  Download the [listen.html](https://github.com/franklinr/amcrestDoorbellHomeAssistant/blob/c9b064a62d80a21b4a400ab8b3d5805623f2b44e/config/www/listen.html) file to that folder.  Go the the address below and you should hear the audio from the doorbell. This html code automatically plays the audio.  Clicking on the Listen button will make it stop.  
https://home.MYMACHINE.synology.me/local/listen.html

7. Download the [speak.html](https://github.com/franklinr/amcrestDoorbellHomeAssistant/blob/a5377c64a736048a1c75fbf3e4e26f88afeb70a7/config/www/speak.html) file to the www folder.  Go to the address below. Clicking on the Speak button should allow you to speak to someone at the doorbell.  This code automatically set up the microphone for speaking.
https://home.MYMACHINE.synology.me/local/speak.html

8. Finally, I have it so that my system will switch to the doorbell dashboard when there is motion in the camera area.
This is done using my [automation.yaml](https://github.com/franklinr/amcrestDoorbellHomeAssistant/blob/bf00faa35c967f0995ffaddd7e65ebf9ea03c996/config/automations.yaml) which requires [browser_mod](https://github.com/thomasloven/hass-browser_mod) to switch dashboards.


