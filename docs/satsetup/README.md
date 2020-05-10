# Satellite setup

::: warning
This is an alpha release! It is subject to major changes and the target audience is people with some knowledge of their devices, coding, installing python modules and basic stuff!
:::


- Download our prefabricated Raspbian buster image:
   - [Direct download](https://github.com/project-alice-assistant/ProjectAliceSatellite/releases/tag/v1.0.0-a1)
   - This image has nothing more than Project Alice and its dependencies as well as Hermes Led Control
   - If you wonder what's check the [Preinstalled Packages Section](preinstalled)

::: tip
You need the AliceSatellite installed and running on your main unit!
:::

- Flash the image to your SD card
- **do not place any wpa_supplicant.conf or ssh file in /boot**
- **do not boot your raspberry before doing this next point!!**
- **Edit the /boot/ProjectAliceSatellite.yaml file**. If there's no ProjectAliceSatellite.yaml file on the /boot partition, grab the latest from sources: [ProjectAliceSatellite.yaml](https://github.com/project-alice-assistant/ProjectAliceSatellite/blob/master/ProjectAliceSatellite.yaml)
- Configure it according to your information and needs. All configuration have a little explanation but most should be self explanatory.
- Make sure to have a space after the colons:
   - Wrong: `forceRewrite:yes`
   - Correct: `forceRewrite: yes`
- If the option value contains any special characters, enclose the whole with double quotes:
   - Wrong: `mqttPassword: a:%qJi`
   - Correct: `mqttPassword: "a:%qJi"`
- Save it, unplug your sd, plug it in your raspberry pi.
- Ask Alice to "Add a new satellite"
- Power up your satellite
- Alice should boot by herself and connect to your wifi, update, configure and start. This may take a while
- If Alice times out for the satellite addition, after 5 minutes, she'll tell you. In that case ask her again to "Add a satellite". If you are using respeakers per exemple, the satellite setup might take more than 5 minutes.
- The SSH User is `pi` and the password is `umbrella`

# Additional steps you may come across if updating to 1.0.0-a2 and alice is using the new audio/server and dialogManager

1. During your initial boot up of the satellite it may shutdown and come up with a error that it can't run the audio service (If your on something other than google voice hat), so in the command line 
``` sudo nano /boot/config.txt``` 
2. Put a # in front of the line ```#dtoverlay=googlevoicehat-soundcard```

3. Back at the command line 
``` git stash```

``` git checkout 1.0.0-a2```

``` git pull```

4. Now you need to update to the new ProjectAlice audio server etc

``` cd ProjectAlice```

``` ./venv/bin/pip install wheel```

``` sudo apt install portaudio19-dev ```

``` ./venv/bin/pip install pyaudio ```

5. if on boot up you get hermesLEDControl errors you may have to install that manually.

``` sudo nano ~/hermesLedControl/install.sh``` 

6. comment out line 24 which says "exit" by putting a # in front of it EG: ```#exit``` then ctrX  then y to save and exit the nano editor

``` sudo bash  ~/hermesLedControl/install.sh``` and follow the on screen prompts, then let it install

7. once finished ```mv ~/hermesLedControl_/venv ~/hermesLedControl```

8. ```sudo nano /etc/systemd/system/hermesledcontrol.service```

9. on this line ```ExecStart=/home/pi/hermesLedControl/venv/bin/python3 main.py --hardware=googleAIY --pattern=projectalice```

   change it to 
   
   ```ExecStart=/home/pi/hermesLedControl/venv/bin/python3 main.py --hardware=googleAIY --pattern=projectalice  --mqttServer=<add your alice IP address>  --mqttPort=1883  --clientId=<add your satellite room name>```
   
   eg:
   
   ```ExecStart=/home/pi/hermesLedControl/venv/bin/python3 main.py --hardware=googleAIY --pattern=projectalice  --mqttServer=192.168.1.1  --mqttPort=1883  --clientId=office```
   
   then save and exit from the nano editor

10. you may now have to setup your /etc/asound.conf file to match requirements found in #hotfixes on discord
 

If everything goes fine, Alice will confirm the addition of the satellite and automatically configure it to connect to the correct Mqtt server. She will also automatically upload the hotword and wakeword resources to the new device.

#### Having issues with Alice? She doesn't hear you? Doesn't seem to start?
Have a look at our [Troubleshooting guide](troubleshooting)!
