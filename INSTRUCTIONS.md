# Instructions for setting up interceptor

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
