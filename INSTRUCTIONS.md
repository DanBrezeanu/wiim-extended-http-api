# Instructions for setting up interceptor

## Using mitmproxy

### Requirements
* Android device (emulators can be used)
* [WiiM Home](https://play.google.com/store/apps/details?id=com.linkplay.wiimhome) app
* [mitmproxy](https://mitmproxy.org)

### Instructions
* Install mitmproxy
* Run `mitmweb --ssl-insecure` from a terminal
* Configure Android to use the proxy:
  * Settings
  * Network & Internet
  * Internet
  * Settings cog (beside WiFi network name)
  * Pencil (in the top right)
  * Open "Advanced options"
  * Change "Proxy" to "Manual"
  * Set "Proxy hostname" to the proxy IP address
  * Set "Proxy port" to 8080
* Filter HTTP requests by searching for "command=".
* Open the WiiM Home app and start clicking various options in the app. The respective HTTP requests will appear in the HTTP interceptor.
  ![mitmweb example](https://github.com/user-attachments/assets/a600b64f-5691-49bc-a05a-7557bff804c8)
* When done, don't forget to change "Proxy" back to "None"


## Using HTTP Toolkit PRO

### Requirements
* Android device (emulators can be used)
* [WiiM Home](https://play.google.com/store/apps/details?id=com.linkplay.wiimhome) app
* [HTTP Toolkit **PRO**](https://httptoolkit.com/) - installed on both the Android device and a PC
  * You need the PRO version in order to allow trusting the self-signed SSL certificate on the WiiM device.
  * The non-PRO version can be used to see the what requests the app makes, but they won't be forwarded to the device.
 
### Instructions

* Make sure the WiiM device is set up in the application
* In HTTP Toolkit on the PC, navigate to Settings -> HOST HTTPS WHITELIST -> Add the IP address of the WiiM device
![image](https://github.com/user-attachments/assets/9be26b98-4528-4d29-b607-99c1d6f9f423)
* Start the HTTP Toolkit with the Android device (via ADB or QR code)
* Filter HTTP requests using the "command=" string.
* Open the WiiM Home app and start clicking various options in the app. The respective HTTP requests will appear in the HTTP interceptor.
![image](https://github.com/user-attachments/assets/ba3be507-e5bd-4dcb-b50f-eba32ab4223e)
