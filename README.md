# Add Inovelli Switch, Dimmer, or Bulb to Home Assistant.
<br><a href="https://www.buymeacoffee.com/Petro31" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-black.png" width="150px" height="35px" alt="Buy Me A Coffee" style="height: 35px !important;width: 150px !important;" ></a>

_Hassio/Home Assistant Inovelli setup for OpenZwave v1.4_

The steps included in this readme will allow you to add Innovelli devices to home assistant.  This will bring over all zwave configuration options and properly name the devices.

# 1.  Setup Home assistant to use custom zwave configurations.

1.  Go to the OpenZwave 1.4 repo and download the entire openzwave config directory.  Click [here](https://github.com/OpenZWave/open-zwave/tree/1.4/config) for a direct link to the entire folder you need to download.  If you want to download all the files in 1 shot, I recomend using the extension [gitzip](https://chrome.google.com/webstore/detail/gitzip-for-github/ffabmkklhbepgcgfonabamgnfafbdlkn?hl=en) for chrome.

2.  Copy the files into your config folder.  I placed the [linked config folder and files](https://github.com/home-assistant/open-zwave/tree/hass/config) into a folder named ozwave residing in the root directdory of your home assistant configuration.  This guide will assume you do the same.  The resulting path will be `/config/ozwave/config/<company name folders & files>`.
 
3.  Copy [these files](https://github.com/Petro31/inovelli_zwave_config/tree/master/config/inovelli) into the `/config/ozwave/config/inovelli` directory.

# 2. Edit `manufacturer_specific.xml`.

1.  Browse to `/config/ozwave/config/` and edit the file named `manufacturer_specific.xml`.

2.  Inside `manufacturer_specific.xml` find (CTRL+F) `Inovelli`.  You are specifically looking for a section that looks like this:

    You're looking for a section named `<Manufacturer id="031E" name="Inovelli">`.  You may stumble on a section named `<Manufacturer id="0312" name="Inovelli">`.  Leave that alone.

    The section should look like this:

```
	<Manufacturer id="031E" name="Inovelli">
	</Manufacturer>
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Change the section to look like this:
```

	<Manufacturer id="031E" name="Inovelli">
		<Product type="0002" id="0001" name="LZW30-SN Switch Red Series" config="inovelli/lzw30-sn.xml"/>
		<Product type="0001" id="0001" name="LZW31-SN Dimmer Red Series" config="inovelli/lzw31-sn.xml"/>
		<Product type="0005" id="0001" name="LZW42 Multi-Color Bulb" config="inovelli/lzw42.xml"/>
		<Product type="0006" id="0001" name="LZW41 Multi-White Bulb" config="inovelli/lzw41.xml"/>
		<Product type="0007" id="0001" name="LZW40 Dimmable  Bulb" config="inovelli/lzw40.xml"/>
	</Manufacturer>
```
   
# 3. Edit `configuration.yaml` zwave section.

Now we need to point home assistant to the openzwave configuration we just created.

Inside `configuration.yaml` edit your zwave section to contain the `config_path` field with the following path `/config/ozwave/config`
   
```
zwave:
  usb_path: /dev/ttyACM0
  config_path: /config/ozwave/config
```
   
# 4. Include your devices to the zwave network.

You may need to exclude your devices if they are already included.  Once you include the devices back into the system.  You should have access to all zwave configurations.  After you include, heal your network and you'll be good to go.

# 5. Device Specifics

## RGB bulbs.

RGB bulbs for some reason do not gather the correct supported features.  In order to gain access to white value, you'll need to change the supported features.

Add the following to your customize section.  You cannot add these values through the UI.  It must be done through yaml files.  See table below to get correct supported_features.

```
homeassistant:
  customize:
    light.inovelli_lzw42_multi_color_bulb_level:
    supported_features: 177
```
You may have set up a separate file for customize.yaml.  If so, the configuration would go in that file.

configuration.yaml
```
homeassistant:
  customize: !include customize.yaml
```
customize.yaml
```
light.inovelli_lzw42_multi_color_bulb_level:
  supported_features: 177
```

Bulb | supported_features
---|---
LZW40| 33
LZW41| 161
LZW42| 177
