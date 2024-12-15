# WiiM Extended HTTP API

In addition to the HTTP endpoints presented in the [official docs](https://www.wiimhome.com/pdf/HTTP%20API%20for%20WiiM%20Products.pdf), the WiiM's HTTP API allows for way more undocumented operations:

| Operation description |   Endpoint  | Examples | Observations |
| :-------------------: | ----------- | -------- | --------------------------------------------------------- |
| Change output to BT device  | /httpapi.asp?command=connectbta2dpsynk:<BT_MAC_ADDRESS> | https://192.168.1.100/httpapi.asp?command=connectbta2dpsynk:9c:2a:be:2e:ce:f1 | The target speaker/device should be paired before connecting | 
