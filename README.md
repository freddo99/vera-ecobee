<!--	Vera Plugin for ecobee Thermostats	-->


![Devices in Vera](http://cocu.la/vera/ecobee/images/shot2.jpg)

**Currently in development**

## Purpose ##
This plugin will monitor and control your [ecobee][] thermostat(s) through your [Vera][] home controller.

[ecobee]: http://www.ecobee.com
[vera]: http://www.micasaverde.com

## Features ##

* Monitor thermostat mode, fan mode, current and set point temperatures, humidity level, current event (if any) and current climate.

* Change HVAC mode and set indefinite holds for temperature set points and fan mode, and allow the program to resume from these holds.

* Use a QuickSave-like hold event (for Si thermostats) or SwitchOccupancy event (for EMS thermostats) to set and monitor an "away" state.

* Perform common functions, such as sending a text message or resuming the program, to an individual thermostat or a group of thermostats.

## How to Use the Plugin ##

First, login to your web portal at [ecobee][] and switch to the [settings tab][].  Familiarize yourself with the portal, and Choose `My Apps` on the left edge of the screen to enable that view.  Leave this browser window open before proceeding to the next step.

[settings tab]: https://www.ecobee.com/home/secure/settings.jsf

### Plugin Authorization with ecobee.com ###

Ecobee uses the [OAuth 2.0](http://oauth.net/) framework for authorizing client applications, such as this plugin.  The practical implication of this is that you do not specify your user name and password in the plugin's settings; instead you grant access by entering a four-character PIN in the ecobee web portal when presented with one by the plugin.  _Please note that one ecobee account can only authorize a single plugin at one time. If you authorize the plugin from a different Vera, it will invalidate any previous authorization of the plugin for that ecobee account._

Upon installing the ecobee plugin, it will attempt to connect with the ecobee.com servers and obtain a four-character PIN in order to authorize the plugin to access your ecobee.com account.  This PIN will be displayed on the Vera dashboard on the ecobee device that was created at installation.

![ecobee device prompting to register PIN at ecobee.com](http://cocu.la/vera/ecobee/images/shot2b.jpg)

Once you see it, you then have ten minutes or less to enter this PIN in the My Apps widget in your ecobee.com web portal.  (Also, you may want to mark your calendar for next year because this PIN authorization expires after one year.  External events may also invalidate the authorization sooner, which will trigger the PIN request process again.)

### Choosing which thermostats to monitor and control ###

_If you are a **non-commercial customer** with one or more ecobee Si thermostats (not EMS model thermostats), the default values should work for you and you can skip this section.  Otherwise, perform this step now._

If you are an Energy Management System (EMS) commercial customer, you must also set values for three variables on the Advanced tab of the ecobee device.

![Advanced tab of the ecobee device](http://cocu.la/vera/ecobee/images/shot2a.jpg)

The variables are `scope`, `selectionType` and `selectionMatch` and must be set according to the following rules:

| `scope` | `selectionType` | `selectionMatch` | examples              |
|---------|-----------------|:-----------------|:----------------------|
| **ems** | **thermostats**   | comma-separated list of thermostat identifiers (no spaces and 25 identifiers maximum) | 276075669054,276181238912 |
| **ems** | **managementSet** | path to the management set of thermostats (these sets are managed through the ecobee.com EMS Management Portal.) | /Washington/Warren/Floor2<br />/ |

If you are non-commercial customer, these variables must be set like this (a `selectionType` of `registered` is the plugin's default setting):

| `scope` | `selectionType` | `selectionMatch` | examples              |
|---------|-----------------|:-----------------|:----------------------|
| **smartWrite** | **thermostats**   | comma-separated list of thermostat identifiers (no spaces and 25 identifiers maximum) | 276075669054,276181238912 |
| **smartWrite** | **registered** | | (`selectionMatch` is empty)|


_Please make sure that you specify the proper upper- and lowercase letters when entering the above variable values._

_If you changed the value of the `scope` variable, also completely empty the `auth_token` field so that the plugin can request a new PIN using the proper account type.

After you have entered the PIN in your My Apps widget at ecobee.com, on the next polling cycle the plugin will attempt to retrieve information about the thermostats that you specified in the selection criteria above.  The plugin will create a thermostat, humidistat and home/away switch device for each thermostat it discovers.  It will name each device as it is named in the thermostat itself, or if there is no name, it will name the device using the thermostat's unique identifier.  You can change this name in the Advanced tab of the device.

## UPnP actions for ecobee Thermostats ##

In addition to the standard thermostat actions, your Vera automation triggers can also perform the following actions on a specific thermostat.  The main ecobee device also implements these actions, in which case the action is applied to all thermostats that match the `selectionType` and `selectionMatch` you specified above.

### ResumeProgram ###

Remove the active event and resume the thermostat's program, or enter the next event in the stack if one exists.

This action has no parameters.

### SendMessage ###

Send a text message to a thermostat's display screen.  The text message can be up to 500 characters long.

| Parameter   | Direction | Value                         |
|:------------|:----------|:------------------------------|
| MessageText | In        | Up to 500 characters of text. |


## Notes and Limitations ##

* Only works with Vera UI5 1.5.408 or later.

* Updates to the state of thermostat and humidistat devices can take up to the polling number of seconds (120 by default) to be reflected in the UPnP devices (or as quickly as 5 seconds).

* One Vera will only maintain a single ecobee.com authentication, so creating multiple instances of the ecobee device will not request multiple PINs for authentication.  This means that a single Vera cannot be used to manage instances of the ecobee device authenticated against multiple ecobee.com accounts.  The other choice was to require that each instance of the ecobee device request a PIN against all different ecobee.com user accounts, which seemed less useful in practice.  So the only scenario that works currently is where a single ecobee.com user wants to authenticate a single Vera, and on that Vera, there can be multiple instances of the ecobee device connected to the same ecobee.com user account (but with different selectionMatches specified in the Advanced tab variables to make doing so useful).  Please contact [me][] if your requirements differ.

[me]: http://forum.micasaverde.com/index.php?action=profile;u=19018

* The humidistat device currently only implements the humidity sensor service.

* The thermostat device creates the `UserSuppliedWattage` variable, set initially to `0,0,0`, but it doesn't yet do anything else to implement the `urn:micasaverde-com:serviceId:EnergyMetering1` service.

## License ##

Copyright &copy; 2013  John W. Cocula and others

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program.  If not, see <http://www.gnu.org/licenses/>

The source code repository and issue list can be located at <https://github.com/watou/vera-ecobee-thermostat>.

House icon copyright [Alexander Moore](http://www.mricons.com/show/iconset:vista-inspirate-icons).

## Feedback  ##

Please contact me through the [micasaverde.com forum][me].  All tips are gratefully accepted!

<div  style="text-align:center">
<form action="https://www.paypal.com/cgi-bin/webscr" method="post">
<input type="hidden" name="cmd" value="_s-xclick">
<input type="hidden" name="hosted_button_id" value="QUZULJ2GPLY7L">
<input type="image" src="https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif" border="0" name="submit" alt="PayPal - The safer, easier way to pay online!">
<img alt="" border="0" src="https://www.paypalobjects.com/en_US/i/scr/pixel.gif" width="1" height="1">
</form>
</div>

## Thanks ##

Thanks to [ecobee][] and their enthusiastic customers, and the helpful contributors on the [Mi Casa Verde forum](http://forum.micasaverde.com).

## Future Plans ##

* See about integrating historical energy usage data into the plugin.
* Report the heating or cooling stage, since ecobee supports multiple heating and cooling stages.
* More automation.
* Monitor and manage ecobee's Smart Plugs as individual `urn:schemas-upnp-org:device:BinaryLight:1` devices when used with Smart thermostats.
* Implement Humidistat functionality to control humidity from Vera.

## History ##

### 2013-0X-XX    v0.5

* Initial plugin upload to <http://apps.mios.com>

