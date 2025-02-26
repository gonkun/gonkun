# Networking


## Tutorials and Guides

### Configure Router Mikrotik Replacing Router HGU Movistar/O2
This guide explains different steps for configure my router Miktrotik hEX S replacing Movistar/O2 router HGU. this one will be used as ONT only.
First I explain how to configure router Mikrotik for our LAN, WLAN, DHCP server, DNS, WireGuard VPN, etc....

Later, I explain how to configure current router Movistar/O2 HGU mode ONT/Bridge
#### Home Network Map
![Home Network Map](./images/home_network.png "Home Network Map")
This is the network map which I configured following this guide.

#### Hard Reset
This is optional. I did it because I configured this router years ago and I didn't remember which CIDR I've configured and credentials for login on it:
1. With unplugged router, press "Reset" button and plug it again.
2. Release the button when green SFP LED starts flashing to reset RouterOS configuration to defaults. [More info](https://help.mikrotik.com/docs/spaces/UM/pages/18350173/hEX+S#hEXS-Powering)
3. After router reboots, I can access to router config with IP `192.168.88.1` and credentials `username: admin` and no password. We need to modify our IP and set another inside CIDR `192.168.88.0/24`

#### Change default IP address
Before start to configure anything, ust to reminder that we can manage a mikrotik router with a GUI like WinBox o WebFig, or using a command line terminal.
I recommend to use WinBox but I added CLI commands for apply same config just doing copy-paste.


##### WinBox
1. Change default IP address
  - Go to `IP` > `Addresses`
  - Select interface with name `bridge`
  - Modify `Address` and `Network`. In this case:
    - `Address: 192.168.2.1/24`
    - `Network: 192.168.2.0`
  - Click on `Apply` and `OK`
  ![Setting default IP](./images/change_default_IP.png)
  - Go to `System` > `Reboot`

##### CLI
1. Change default IP address
  - Get list of Address on interfaces<br>
    ```
    ip/address/print
    ```
  - Modify IP for `bridge` interface which has id `0`
    ```
    ip/address/set numbers=0 address=192.168.2.1/24
    asdasda
    ```
  - Check if change has be done
    ```
    ip/address/print
    ```
  - Reboot router to force getting new IP
    ```
    system/reboot
    ```
