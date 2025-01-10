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
  * Args: `3` (Not sure what this argument means, I've only seen the app use the value `3` for it. Works with other integers and successfully starts a BT scan. Maybe a max list size?)
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
   
