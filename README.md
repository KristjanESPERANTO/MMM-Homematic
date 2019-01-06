# MMM-Homematic
HomeMatic Module for MagicMirror

This an extension for [MagicMirror](https://github.com/MichMich/MagicMirror) that shows values from [HomeMatic](https://www.homematic.com/) smart home components.

This module makes use of the [XML-API](https://github.com/hobbyquaker/XML-API), which must be installed on your HomeMatic CCU to read the sensor values from.

![screenshot](screenshot.png)

## Using the module

To use this module, add it to the modules array in the `config/config.js` file:
````javascript
modules: [
	{
		module: 'MMM-Homematic',
		position: 'top_center',
		header: 'SMART HOME',
		config:	{
			ccuHost: 'ccu3-webui',	// hostname of your ccu (e.g. for CCU3 default is "ccu3-webui")
			tempUnit: "°C",			// unit of your temperatur values
			datapoints: [			// the datapoints of your HomeMatic devices/sensors
				{
					id: "2297",
					name: "window Living Room",
					type: "window_warn_open"
				},
				{
					id: "1274",
					name: "humidity Laundry Room",
					type: "hum_warn_high",
					threshold: "60"
				},
				{
					id: "1264",
					name: "temperatur Laundry Room",
					type: "temp_warn_low",
					threshold: "10"
				}
			]
		}
	}
]
````

HomeMatic is a registered trademark of [eQ-3 AG](https://www.eq-3.de/)

## Howto get your datapoint IDs

* Install the XML-API on your HomeMatic CCU. Installation guide can be found here: [XML-API](https://github.com/hobbyquaker/XML-API)

* Call the list of devices via the XML-API using http://ccu3-webui/addons/xmlapi/devicelist.cgi, replacing 'ccu3-webui' with the hostname or IP address of your CCU.

* Find the ise_id of your device in the output, which may look like this:
````
...
<device name="window contact living room" address="001098A98A1C03" ise_id="2086" interface="HmIP-RF" device_type="HmIP-SWDO-I" ready_config="true">
<channel name="window contact living room:0" type="30" address="001098A98A1C03:0" ise_id="2087" direction="UNKNOWN" parent_device="2086" index="0" group_partner="" aes_available="false" transmission_mode="AES" visible="true" ready_config="true" operate="true"/>
<channel name="HmIP-SWDO-I 001098A98A1C03:1" type="37" address="001098A98A1C03:1" ise_id="2115" direction="SENDER" parent_device="2086" index="1" group_partner="" aes_available="false" transmission_mode="AES" visible="true" ready_config="true" operate="true"/>
</device>
...
````
In this case we are looking for the ise_id of the device "window contact living room", which is "2086".

* Call the state of the device via the XML-API using http://ccu3-webui/addons/xmlapi/state.cgi?device_id=1234, replacing 'ccu3-webui' with the hostname or IP address of your CCU and '1234' with the ise_id from the previous step.

* Find the ise_id of the desired datapoint of your device in the output.
<br>For window/door contact sensors it is the datapoint of type="STATE", for temperature sensors it is the datapoint with the type="ACTUAL_TEMPERATURE" and for humidity sensors its type="HUMIDITY".
<br>The output may look like this:
````
<state>
<device name="window contact living room" ise_id="2086" unreach="false" config_pending="false">
<channel name="window contact living room:0" ise_id="2087">
...
</channel>
<channel name="HmIP-SWDO-I 001098A991646A:1" ise_id="2296">
<datapoint name="HmIP-RF.001098A991646A:1.STATE" type="STATE" ise_id="2297" value="0" valuetype="16" valueunit="""" timestamp="1546779254"/>
</channel>
</device>
</state>
````
In this case we are looking for the ise_id of the datapoint of type="STATE" of the device "window contact living room", which is "2297".
<br>Or the output looks like this:
````
<state>
<device name="climate sensor laundry room" ise_id="1238" unreach="false" config_pending="false">
<channel name="climate sensor laundry room:0" ise_id="1239">
...
</channel>
<channel name="HmIP-STHD 000E98A991C6C7:1" ise_id="1262">
<datapoint name="HmIP-RF.000E98A991C6C7:1.ACTIVE_PROFILE" type="ACTIVE_PROFILE" ise_id="1263" value="1" valuetype="16" valueunit="" timestamp="1546779623"/>
<datapoint name="HmIP-RF.000E98A991C6C7:1.ACTUAL_TEMPERATURE" type="ACTUAL_TEMPERATURE" ise_id="1264" value="16.800000" valuetype="4" valueunit="" timestamp="1546779623"/>
<datapoint name="HmIP-RF.000E98A991C6C7:1.ACTUAL_TEMPERATURE_STATUS" type="ACTUAL_TEMPERATURE_STATUS" ise_id="1265" value="0" valuetype="16" valueunit="" timestamp="1546779623"/>
...
</channel>
</device>
</state>
````
In this case we are looking for the ise_id of the datapoint of type="ACTUAL_TEMPERATURE" of the device "climate sensor laundry room", which is "1264".

* Use the ise_id from the previous step as ID for your datapoint in the module config.

## Tested devices

<table width="100%">
  <thead>
    <tr>
      <th>Type</th>
      <th width="100%">Description</th>
    </tr>
  <thead>
  <tbody>
    <tr>
	  <td>window</td>
	  <td>Homematic IP Window / Door Contact (HmIP-SWDO-I)</td>
	</tr>
    <tr>
	  <td>temp</td>
	  <td>Homematic IP Temperature and Humidity Sensor with Display (HmIP-STHD)</td>
	</tr>
    <tr>
	  <td>hum</td>
	  <td>Homematic IP Temperature and Humidity Sensor with Display (HmIP-STHD)</td>
	</tr>
  </tbody>
</table>

## Configuration options

|Option|Description|
|------|-----------|
|<code>ccuProtocol</code>|The protocol to use for your CCU.
Most likely default value is good.
<b>Possible values:</b> <code>http://</code> - <code>https://</code>
<b>Default value:</b> <code>http://</code>|
|<code>ccuHost</code>|The hostname of your CCU.
		<br>Depends on your version of CCU.
		<br>For CCU1 you have to give your CCU a fixed IP address and use it.
		<br>For CCU2 it is most likely "homematic-ccu2".
		<br>For CCU3 it is most likely "ccu3-webui" (default).
		<br>For RaspberryMatic it is the hostname you have set in the network settings of your Raspberry PI.
		<br><b>Default value:</b> <code>ccu3-webui</code>|
|<code>ccuXmlApiUrl</code>|The URL path of the XML API.
	    <br>Most likely default value is good.
	    <br>But may change with newer versions of XML API, see
		[XML-API](https://github.com/hobbyquaker/XML-API).
	    <br><b>Default value:</b> <code>/addons/xmlapi</code>|
|<code>ccuServiceUrl</code>|The name of the XML API Service for getting states/values from devices/datapoints.
	    <br>Most likely default value is good.
	    <br>But may change with newer versions of XML API, see [XML-API] (https://github.com/hobbyquaker/XML-API).
	    <br><b>Default value:</b> <code>/state.cgi</code>|
|<code>ccuIdParameter</code>|The name of the URL Parameter expected by the XML API Service to identify a datapoint of a device.
	    <br>Most likely default value is good.
	    <br>But may change with newer versions of XML API, see [XML-API] (https://github.com/hobbyquaker/XML-API).
	    <br><b>Default value:</b> <code>?datapoint_id=</code>|
|<code>tempUnit</code>|The unit of temperature.
        <br><b>Possible values:</b> <code>°C</code> - <code>°F</code> - <code>K</code>
        <br><b>Default value:</b> <code>°C</code>|
|<code>humUnit</code>|The unit of humidity.
        <br><b>Possible values:</b> <code>%</code> - <code>g/m³</code>
        <br><b>Default value:</b> <code>%</code>|
|<code>datapoints</code>|An array of datapoint objects.
		<br>Each datapoint object represents one value/state of a device.
		<br><b>Example value:</b>
		<br><code>[{
		<br>id: 1234,
		<br>name: "front door",
		<br>type: window_warn_open,
		<br>},{
		<br>id: 4711,
		<br>name: "humidity laundry room",
		<br>type: "hum_warn_high",
		<br>threshold: "60"
		<br>}]</code>|
|<code>id</code>|The ID of the datapoint to read a value from.
	  <br>This value is required.
	  <br>On howto get your ID see [Howto get your datapoint IDs] (#howto-get-your-datapoint-ids).
	  <br><b>Example value:</b> <code>1234</code>|
|<code>name</code>|The display name of the device/datapoint.
	  <br>This value is required.
	  <br><b>Example values:</b> <code>
	  <br>"front door"
	  <br>"temperature living room"</code>|
|<code>type</code>|The type of the device/datapoint.
	  <br>This value is required.
	  <br>Depends on the datapoint/device you want to display.
	  <br>For a list of tested devices see [Tested devices] (#tested-devices).
	  <br><b>Possible values:</b>
	  <br><code>window</code> - A door or window sensor. (e.g. a Homematic IP Window / Door Contact)
	  <br><code>window_warn_open</code> - Same as 'window', but with a warning if open.
	  <br><code>window_warn_closed</code> - Same as 'window', but with a warning if closed.
	  <br><code>temp</code> - A temperature sensor. (e.g. a Homematic IP Temperature and Humidity Sensor with Display)
	  <br><code>temp_warn_high</code> - Same as 'temp', but with a warning if value is equal or greater than the threshold.
	  <br><code>temp_warn_low</code> - Same as 'temp', but with a warning if value is equal or less than the threshold.
	  <br><code>hum</code> - A humidity sensor. (e.g. a Homematic IP Temperature and Humidity Sensor with Display)
	  <br><code>hum_warn_high</code> - Same as 'hum', but with a warning if value is equal or greater than the threshold.
	  <br><code>hum_warn_low</code> - Same as 'hum', but with a warning if value is equal or less than the threshold.
	  <br><code>other</code> - A general sensor with a readable number value.
	  <br><code>other_warn_high</code> - Same as 'other',but with a warning if value is equal or greater than the threshold.
	  <br><code>other_warn_low</code> - Same as 'other',but with warning if value is equal or less than the threshold.|
|<code>threshold</code>|A threshold value for displaying a warning.
	  <br>This value is required if you have defined a type with a high/low warning.
	  <br>Must be a number without unit.
      <br><b>Example value:</b> <code>60</code>|
|<code>initialLoadDelay</code>|The initial delay before loading. If you have multiple modules that use the same API key, you might want to delay one of the requests. (Milliseconds)<br>
        <br><b>Possible values:</b> <code>1000</code> - <code>5000</code>
        <br><b>Default value:</b>  <code>0</code>|
|<code>updateInterval</code>|How often does the content needs to be fetched? (Milliseconds)<br>
        <br>Forecast.io enforces a 1,000/day request limit, so if you run your mirror constantly, anything below 90,000 (every 1.5 minutes) may require payment information or be blocked.<br>
        <br><b>Possible values:</b> <code>1000</code> - <code>86400000</code>
        <br><b>Default value:</b> <code>300000</code> (5 minutes)|
|<code>animationSpeed</code>|Speed of the update animation. (Milliseconds)<br>
        <br><b>Possible values:</b><code>0</code> - <code>5000</code>
        <br><b>Default value:</b> <code>2000</code> (2 seconds)|
