# WiiM Extended HTTP API

In addition to the HTTP endpoints presented in the [official docs](https://www.wiimhome.com/pdf/HTTP%20API%20for%20WiiM%20Products.pdf), the WiiM's HTTP API allows for way more undocumented operations.

If any functionality you need is not listed here, feel free to create a PR with additional info. The instructions on how to set up the interceptor are specified [here](https://github.com/DanBrezeanu/wiim-extended-http-api/blob/main/INSTRUCTIONS.md).

**All HTTP requests use the GET method, regardless whether they set or get a resource.**

---

### Network

* #### Get static network config
  * Command: `getStaticIpInfo`
  * Args: None
  * Example: https://192.168.1.100/httpapi.asp?command=getStaticIpInfo
  * Output example:
    ```json
    {
      "wlanStaticIp": "192.168.100/24",
      "wlanGateWay": "192.168.1.1",
      "wlanDnsServer": "8.8.8.8",
      "wlanStaticIpEnable": 1,
      "ethStaticIp": "",
      "ethGateWay": "",
      "ethDnsServer": "",
      "ethStaticIpEnable": 0
    }
    ```
  * Observations
      * If the WiiM device is configured to use DHCP, the above string values will be empty (see the ethernet related keys).
  
* #### Set static WLAN network config
    * Command: `setWlanStaticIp`
    * Keyword args:
         * `ipAddr`: Static IP address to set + netmask
         * `gateWay`: Gateway
         * `dnsServer`: The DNS Server (default: `8.8.8.8`)
    * Example: https://192.168.1.100/httpapi.asp?command=setWlanStaticIp:ipAddr=192.168.1.100/24:gateWay=192.168.1.1:dnsServer=8.8.8.8
    * Output:
      * Success: `OK`

---

### Bluetooth

* #### Start Bluetooth device scan
  * Command: `startbtdiscovery`
  * Args: `NUM_SECONDS`: Number of seconds for which to run the scan for
  * Example: https://192.168.1.100/httpapi.asp?command=startbtdiscovery:3
  * Output:
    * Success: `OK`

* #### Get Bluetooth device scan result
  * Command: `getbtdiscoveryresult`
  * Args: None
  * Example: https://192.168.1.100/httpapi.asp?command=getbtdiscoveryresult
  * Output (JSON):
      * `num`: Number of found devices
      * `scan_status`: BT scan step
          * `0`: Not started
          * `1`: Initializing
          * `2`: ??? (never seen it)
          * `3`: Scanning
          * `4`: Finished scanning
      * `list`: List of found devices
  * Output example:
    ```json
    { "num": 1, "scan_status": 3, "list": [ { "name": "Bedroom TV", "ad": "1d:a2:53:1a:85:9c", "ct": 0, "role": "Audio Source" } ] }
    ```
  * Observations:
    * Already paired devices may be listed
    
* #### Clear Bluetooth device scan result
  * Command: `clearbtdiscoveryresult`
  * Args: None
  * Example: https://192.168.1.100/httpapi.asp?command=clearbtdiscoveryresult
  * Output:
    * Success: `OK`

* #### Get paired Bluetooth devices
  * Command: `getbthistory`
  * Args: None
  * Example: https://192.168.1.100/httpapi.asp?command=getbthistory
  * Output (JSON):
      * `num`: Number of found devices
      * `scan_status`: BT scan step
          * `1`: Initializing
          * `2`: ??? (never seen it)
          * `3`: Scanning
          * `4`: Finished scanning
      * `list`: List of found devices

  * Output example:
    ```json
     { "num": 1, "scan_status": 0, "list": [ { "name": "My Speaker", "ad": "9c:2a:be:2e:ce:f1", "ct": 0, "role": "Audio Sink" } ] }
    ```

* #### Connect to Bluetooth device
  * Command: `connectbta2dpsynk`
  * Args: `BT_MAC_ADDRESS`
  * Example: https://192.168.1.100/httpapi.asp?command=connectbta2dpsynk:9c:2a:be:2e:ce:f1
  * Output:
    * Success: `OK`
    * Error: `Failed`
  * Observations:
    * The device should be an `Audio Sink` (e.g. speaker, A/V reciever). The audio output will be changed to the device.
   
* #### Disconnect from Bluetooth device
  * Command: `disconnectbta2dpsynk`
  * Args: `BT_MAC_ADDRESS`
  * Example: https://192.168.1.100/httpapi.asp?command=disconnectbta2dpsynk:9c:2a:be:2e:ce:f1
  * Output:
    * Success: `OK`
   
* #### Get Bluetooth pairing status
  * Command: `getbtpairstatus`
  * Args: None
  * Example: https://192.168.1.100/httpapi.asp?command=getbtpairstatus
  * Output (JSON):
      * `result`: Pair status
        * `1`: Disconnected
        * `2`: Connecting (assumption, never seen it)
        * `3`: Connected
  * Output example:
    ```json
    {"result":3}
    ```
---

### Audio

* #### Get SPDIF sample rate switch latency 
  * Command: `getSpdifOutSwitchDelayMs`
  * Args: None
  * Example: https://192.168.1.100/httpapi.asp?command=getSpdifOutSwitchDelayMs
  * Output example
    ```
    800
    ``` 
  * Observations:
    * This value only makes sense if the output interface is SPDIF (optical out).
    * Value can be retrieved even if output is not SPDIF
   
* #### Set SPDIF sample rate switch latency 
  * Command: `setSpdifOutSwitchDelayMs`
  * Args: `SPDIF_OUT_SWITCH_DELAY_MS`
  * Example: https://192.168.1.100/httpapi.asp?command=setSpdifOutSwitchDelayMs:800
  * Output:
    * Success: `OK`
  * Observations:
    * Value can be set even if output is not SPDIF
    * `SPDIF_OUT_SWITCH_DELAY_MS` is upper bound to 3000.

* #### Get left/right channel balance
  * Command: `getChannelBalance`
  * Args: None
  * Example: https://192.168.1.100/httpapi.asp?command=getChannelBalance
  * Output example
    ```
    0.0
    ```
  * Observations:
    * The value will be a number ranging from `-1.0` (fully panned left) to `1.0` (fully panned right).
* #### Set left/right channel balance
  * Command: `setChannelBalance`
  * Args: `CHANNEL_BALANCE`: `-1.0` to `1.0` (decimal is optional)
  * Example: https://192.168.1.100/httpapi.asp?command=setChannelBalance:0
  * Output
    * Success: `OK`
    * Error: `Failed`
---

### Equalizer

* #### Disable Equalizer
  * Command: `EQOff`
  * Args: None
  * Output: `OK`

* #### Get list of available Equalizer presets
  * Command: `EQGetList`
  * Args: None
  * Output (JSON): a list of equalizer presets
  * Output example:
    ```json
    [
        "Acoustic",
        "Bass Booster",
        "Bass Reducer",
        "Classical",
        "Dance",
        "Deep",
        "Electronic",
        "Flat",
        "Game",
        "Hip-Hop",
        "Jazz",
        "Latin",
        "Loudness",
        "Lounge",
        "Movie",
        "Piano",
        "Pop",
        "R&B",
        "Rock",
        "Small Speakers",
        "Spoken Word",
        "Treble Booster",
        "Treble Reducer",
        "Vocal Booster"
    ]
    ```

* #### Set Equalizer preset
  * Command: `EQLoad`
  * Args: one of the preset names
  * Examples:
    * https://192.168.1.100/httpapi.asp?command=EQLoad:Hip-Hop
    * https://192.168.1.100/httpapi.asp?command=EQLoad:Bass+Booster
  * Output (JSON): the resulting 10-band equalizer configuration
  * Output example:
    ```json
    {
      "EQStat": "On",
      "Name": "Classical",
      "pluginURI": "http://moddevices.com/plugins/caps/Eq10HP",
      "EQBand": [
          {
              "index": 0,
              "param_name": "band31Hz",
              "value": 4.8
          },
          {
              "index": 1,
              "param_name": "band63Hz",
              "value": 3.8
          },
          {
              "index": 2,
              "param_name": "band125Hz",
              "value": 3
          },
          {
              "index": 3,
              "param_name": "band250Hz",
              "value": 2.5
          },
          {
              "index": 4,
              "param_name": "band500Hz",
              "value": -1.5
          },
          {
              "index": 5,
              "param_name": "band1kHz",
              "value": -1.5
          },
          {
              "index": 6,
              "param_name": "band2kHz",
              "value": 0
          },
          {
              "index": 7,
              "param_name": "band4kHz",
              "value": 2.2
          },
          {
              "index": 8,
              "param_name": "band8kHz",
              "value": 3.2
          },
          {
              "index": 9,
              "param_name": "band16kHz",
              "value": 3.8
          }
      ],
      "channelMode": "Stereo",
      "status": "OK",
      "source_name": "wifi"
    }
    ```

---

### Squeezelite (Lyrion Music Server/Logitech Media Server) integration

* #### Get information about the Squeezelite configuration
  * Command: `Squeezelite:getState`
  * Args: None
  * Output (JSON):
    * `default_server`: the LMS instance to which the player would connect automatically
    * `state`: 
      * `discovering`: player is discovering LMS instances
      * `connected`: player is connected to an LMS
    * `discover_list`: list of LMS instances found in the player's network
    * `auto_connect`: whether to automatically re-connect to the previously connected LMS instance
  * Output example:
    ```json
    {
      "default_server": "192.168.171.4:3483",
      "state": "discovering",
      "discover_list": [
        "192.168.171.4:3483",
        "192.168.171.24:3483"
      ],
      "connected_server": "192.168.171.4:3483",
      "auto_connect": 1
    }
    ```

* #### Trigger Lyrion Music Server discovery
  * Command: `Squeezelite:discover`
  * Args: None
  * Output: `OK`

* #### Turn on/off LMS auto connect
  * Command: `Squeezelite:autoConnectEnable`
  * Args: `ON`: 0 or 1
  * Examples:
    * https://192.168.1.100/httpapi.asp?command=Squeezelite:autoConnectEnable:0
    * https://192.168.1.100/httpapi.asp?command=Squeezelite:autoConnectEnable:1
  * Output: `OK`


* #### Connect player to previously discovered LMS instance
  * Command: `Squeezelite:connectServer`
  * Args: `LMS IP Address`
  * Examples:
    * https://192.168.1.100/httpapi.asp?command=Squeezelite:connectServer:192.168.1.123
  * Output: `OK`

---

### Misc

* #### Turn on/off status LED ("Status Light" option from app)
  * Command: `LED_SWITCH_SET`
  * Args: `ON`: 0 or 1
  * Examples:
    * https://192.168.1.100/httpapi.asp?command=LED_SWITCH_SET:0
    * https://192.168.1.100/httpapi.asp?command=LED_SWITCH_SET:1
  * Output:
    * Success: `OK`
   
* #### Turn on/off touch controls
  * Command: `Button_Enable_SET`
  * Args: `ON`: 0 or 1
  * Examples:
    * https://192.168.1.100/httpapi.asp?command=Button_Enable_SET:0
    * https://192.168.1.100/httpapi.asp?command=Button_Enable_SET:1
  * Output:
    * Success: `OK`
   
