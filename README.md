#gsm-modem-connection

This modem help to manage gsm modem connection and disconnections.

If you want to write a program for a 3G key in order to send SMS/MMS, make
voicemail or any other usage first you need to switch the key from CD mode 
to normal mode, this is done automatically by usb_modeswitch integrated to most
distribution but after you still need more information in order to user your 3G key.

In normal mode your modem will give you access to several serial devices eg
/dev/ttyUSB1, /dev/ttyUSB2, /dev/ttyUSB3. What this module does is that it 
will cast an event when a 3G modem is connected and tell you: 
"The atPort of your modem is /dev/ttyUSB2 and the audio port is /dev/ttyUSB3"
so you can start using your devices. 

So far it only support Huawei 3G keys but you can update *./knownModem.json*
to make sure your device will be supported.

#install

````bash
git clone https://github.com/garronej/gsm-modem-connection
cd gsm-modem-connection
npm install
````
Installing this package will set udev rules in */etc/udev/rules.d/99-ts-gsm-connection.rule*

#uninstall

To remove udev rules and other files generated by the module:

````bash
npm run uninstall
````

#Usage Example

Example: *./src/test/main.ts* 

Run: npm test

````javascript
import { ModemWatcher } from "../lib/index";

let modemWatcher = new ModemWatcher();

console.log("Awaiting GSM modem connections...");

modemWatcher.evtConnect.attach(modem => console.log("CONNECT", modem.infos));

modemWatcher.evtDisconnect.attach(modem => console.log("DISCONNECT", modem.infos));
````

Output: 

After connecting a E169 and a E160 Huawei 3G key:

````bash
Awaiting GSM modem connections...
CONNECT { vendorIdHex: '0x12d1',
  modelIdHex: '0x1001',
  isKnowModel: true,
  rpiPort: 4,
  atInterface: '/dev/ttyUSB2',
  audioInterface: '/dev/ttyUSB1',
  isFullyBooted: true }
CONNECT { vendorIdHex: '0x12d1',
  modelIdHex: '0x1003',
  isKnowModel: true,
  rpiPort: 3,
  atInterface: '/dev/ttyUSB4',
  audioInterface: '/dev/ttyUSB3',
  isFullyBooted: true }
````

#Standalone usage

This module can be used as standalone it maintain a file ( default */tmp/setModem.json* ) that describe
the modem currently connected to the computer.

````json
{
  "modemDescriptors": [
    {
      "id": "platform-3f980000.usb-usb-0:1.4:1.x",
      "vendorId": 4817,
      "modelId": 4097,
      "upSince": "Tue Jan 17 2017 04:37:41 GMT+0000 (UTC)",
      "interfaces": {
        "0": "/dev/ttyUSB0",
        "1": "/dev/ttyUSB1",
        "2": "/dev/ttyUSB2"
      },
      "readableInfos": {
        "vendorIdHex": "0x12d1",
        "modelIdHex": "0x1001",
        "isKnowModel": true,
        "rpiPort": 4,
        "atInterface": "/dev/ttyUSB2",
        "audioInterface": "/dev/ttyUSB1",
        "isFullyBooted": true
      }
    },
    {
      "id": "platform-3f980000.usb-usb-0:1.3:1.x",
      "vendorId": 4817,
      "modelId": 4099,
      "upSince": "Tue Jan 17 2017 04:38:33 GMT+0000 (UTC)",
      "interfaces": {
        "0": "/dev/ttyUSB3",
        "1": "/dev/ttyUSB4"
      },
      "readableInfos": {
        "vendorIdHex": "0x12d1",
        "modelIdHex": "0x1003",
        "isKnowModel": true,
        "rpiPort": 3,
        "audioInterface": "/dev/ttyUSB3",
        "isFullyBooted": true
      }
    }
  ],
  "bootId": "d48dc484-0ab7-459d-b876-5c9dd53d0316"
}
````

#Configuration

Edit *./config.json*

#Add specific support for your modem ( 3G key )

Edit *./knownModem.json*, any pull request welcome.

To support a modem you need to know, total how many serial interface it create ( usually 2 or 3 )
and what USB component is associated to audio ant to AT command.

If you don't know how to retrieve those information just post an issue and I will, with your help, add support for your modem
as quickly as possible.

Find information relative to your hardware at this page:
[List of USB ID's](http://www.linux-usb.org/usb.ids)



#note

After editing the .json file uninstall and reinstall the module for the change to take effect.

````bash 
npm run uninstall
npm install
````
