
l33tport
======


#### Downloads JSON status data from Telekom Speedport Hybrid

Please note this is a fork of the l33tport https://github.com/melle/l33tport. Any reduction in quality are fully on me.


You own a Telekom Speedport Router? The web-interface sucks? It forces you to login every fucking time? You want to draw fancy rrdtool-graphs from raw data?

This script helps you to access the status information that is available in the "hidden" [Engineer-Menu](http://speedport.ip/engineer/html/dsl.html?lang=en), which is only available after you perform a login on the bloated web interface.

![The engineer menu of the Telekom Speedport Hybrid.](assets/EngineerMenu.jpg)

Prerequisites
==========
l33tport was written using node.js, so you need to have a working node installation. For the crypto-foo we're are using the [Stanford Javascript Crypto Library](https://github.com/bitwiseshiftleft/sjclsjcl.js), for commandline parsing commander is needed:

```
npm install sjcl
npm install commander
```

Usage
=====

Adjust the address (Usually ```speedport.ip``` / ```192.168.2.1```) and the password in the header of the script.

Usage: speedport [options]

Options:

    -h, --help                   output usage information
    -V, --version                output the version number
    -o, --output <format>        Output format (json, rrd)
    -d, --dsNames <dsNames>      rrdtool update format specifier
    -f, --fieldname <fieldname>  specifies the status field


You may use one of the following fieldnames (i.e. -f dsl):

* **dsl**              DSL connection status and line information
* **interfaces**       Network interfaces
* **arp**              ARP table
* **session**          PPPoE Session
* **dhcp_client**      DHCP client
* **dhcp_server**      DHCP server, includes DHCP leases 
* **ipv6**             IPv6 Router Advertisement
* **dns**              DNS server and cache information
* **routing**          Routing table
* **igmp_proxy**       IGMP Proxy
* **igmp_snooping**    IGMP Snooping Table
* **wlan**             WLAN status and information
* **module**           Software version information
* **memory**           Memory and CPU utilization
* **speed**            Speed dial
* **webdav**           WebDAV URL
* **bonding_client**   Bonding HA client
* **bonding_tunnel**   Bonding tunnel
* **filterlist**       Filter list table
* **bonding_tr181**    Bonding TR-181
* **lteinfo**          LTE information
* **Status**           Systeminformation (no login needed)
* **SecureStatus**     Secure system information (login needed)
* **Overview**         General status information, i.e. tunnel status
* **modules**
* **Abuse**            trusted SMTP servers configuration
* **DECTStation**      DECT configuration
* **hsdelmobil**       DECT handset status
* **LAN**              LAN status (DHCP assigned IPs ect.)


Examples
========

Download dsl status file and print content in JSON format:

    $ node ./l33tport.js -f dsl

The result will look like this:

```
{ Connection: 
   { dsl_operaing_mode: 'ADSL_2plus',
     path_mode: 'None',
     state: 'Up',
     training_results: 'Showtime',
     mode_lo: 'L0',
     vpi_vci: '1/32' },
  Line: 
   { uactual: '1167',
     dactual: '13551',
     uattainable: '1368',
     dattainable: '13524',
     uSNR: '109',
     dSNR: '59',
     uSignal: '130',
     dSignal: '0',
     uLine: '210',
     dLine: '420',
     uBIN: '512',
     dBIN: '512',
     uFEC_size: '1',
     dFEC_size: '1',
     uCodeword: '0',
     dCodeword: '0',
     uInterleave: '0',
     dInterleave: '0',
     uCRC: '4',
     dCRC: '6901',
     uHEC: '2',
     dHEC: '30865',
     uFEC: '0',
     dFEC: '0' } }
```

rrdtool integration
=============

![A DSL line with lots of errors.](assets/dsl-48h.png)

l33tport 's output may be formatted to fit as input for 'rddtool update' command. The json data delivered by the router has a tree structure that can contain list of elements (like in the bonding_tunnel case).
The formating parameter accepts mapping between the JSON data and the rrdtool format:
Example: 

-d "Line.uSNR=uSNR" picks up the uSNR data in the Line Object in the above DSL example

This is more interesting for list cases like in the bonding tunnel

{"TcpExt":[{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"12"},{"TcpExt":"6"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"2789"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"155848"},{"TcpExt":"35"},{"TcpExt":"84"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"3"},{"TcpExt":"0"},{"TcpExt":"1824"},{"TcpExt":"0"},{"TcpExt":"625151"},{"TcpExt":"2"},{"TcpExt":"33382"},{"TcpExt":"699867"},{"TcpExt":"0"},{"TcpExt":"8"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"1"},{"TcpExt":"38"},{"TcpExt":"1"},{"TcpExt":"0"},{"TcpExt":"248"},{"TcpExt":"68"},{"TcpExt":"9"},{"TcpExt":"0"},{"TcpExt":"203"},{"TcpExt":"484"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"84"},{"TcpExt":"0"},{"TcpExt":"44"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"5"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"19"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"10"},{"TcpExt":"21"},{"TcpExt":"326"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"},{"TcpExt":"0"}],"IpExt":[{"IpExt":"1"},{"IpExt":"0"},{"IpExt":"52935"},{"IpExt":"4856"},{"IpExt":"9282"},{"IpExt":"0"},{"IpExt":"997837387"},{"IpExt":"1045975015"},{"IpExt":"9829342"},{"IpExt":"183551"},{"IpExt":"1944800"},{"IpExt":"0"}],"ireg":[{"ireg":"20864551"},{"ireg":"0"},{"ireg":"20857770"},{"ireg":"47"},{"ireg":"0"},{"ireg":"0"},{"ireg":"0"},{"ireg":"1193090"},{"ireg":"309"},{"ireg":"1"},{"ireg":"354590"},{"ireg":"0"},{"ireg":"0"},{"ireg":"0"},{"ireg":"0"}],"lte_tunnel":"Up","dsl_tunnel":"Up","bonding":"Up","ipv4":"87.135.132.22","subnet_mask":"255.255.128.0","ipv6_pre":"2003:0006:1404:c700::/56"}


-d "IpExt.6.IpExt=InOctets" picks ups the the 6th entry in the IpExt List of Objects, which is the field that is labeled InOctets in the WebUI (view the source code of the page)

    $ ./l33tport.js -f dsl -o rrd -d "Line.uSNR=uSNR,Line.dSNR=dSNR,Line.uactual=uactual,Line.dactual=dactual,Line.uatainable=uatainable,Line.dattainable=dattainable"

The output looks like this:

    --template uSNR:dSNR:uactual:dactual:uatainable:dattainable N:1167:13551:13468:109:59
   
It may be fed directly into a ```rrdtool update``` call:

    rrdtool update dsl.rrd $(./l33tport.js -f dsl -o rrd -d "Line.uSNR=uSNR,Line.dSNR=dSNR,Line.uactual=uactual,Line.dactual=dactual,Line.uatainable=uatainable,Line.dattainable=dattainable")

See the ```rrdtool``` directory for sample scripts.
