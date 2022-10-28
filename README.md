# flex6k-discovery-util-go


Util to use FRS 6XXX(R) signature series radios across subnets / routed VPNs

Currently tested on RasberryPi, X64 Linux, and Windows

## Download precompiled for your system

Most recent binary release  v2022.2 is here (Precompiled for Windows,Linux,pfSense & Raspberry PI or any other ARMv5 compatible SBC):
https://github.com/hb9fxq/flex6k-discovery-util-go/releases/tag/v0.1

...currently for 386/amd64 Linux (ubuntu, etc and Windows) FreeBSD (PfSense) and ARM 5 linux (RaspberryPi)
If your platform isn't listed, send me a pull request for the pretty simple build.sh file. 

## Example Usage

How it works: You need a client and a server which establish a link. One of them has to be installed in the subnet where your radio is connected and the other where SmartSDR runs. 
It does not matter where you install the client and where the server.

### Simple setup (Client/Server):

* Server: 192.168.1.4 is on a VPN site with [n] radios in the subnet
* Client: 10.147.20.144 is a VPN router, at home 192.168.92.0/40
* 192.168.1.7 Radio in this example

#### Server VPN/Network site (server is installed close to the radio)
Linux command:</br>
```
./flexi --SERVERIP=192.168.1.4 --SERVERPORT=7777
```
Windows command:Linux command:</br>
```
flexi -SERVERIP=192.168.1.4 -SERVERPORT=7777
```

#### Client VPN/Network site (Client in the subnet of SmartSDR)


Linux command:</br>
```
./flexi --REMOTES=192.168.1.4:7777 --LOCALIFIP=10.147.20.144 --LOCALPORT=7788
```

Windows command:</br>
```
flexi -REMOTES=192.168.1.4:7777 -LOCALIFIP=10.147.20.144 -LOCALPORT=7788
```

Hint: When the program is run for the first time, Windows will ask to for permission to add a firewall rule. Make sure to select your network (Public&Private as needed) to allow the tool to communicate!

The ports can be changed. SERVERPORT and REMOTES port has to be the same (here 7777)

If you execute these two commands the following output should result:</br>
Client side:
```CLT RECEIVED PKG FROM SRV @ 192.168.1.4
    broadcasting in local subnet
CLT RECEIVED PKG FROM SRV @ 192.168.1.4
    broadcasting in local subnet
CLT RECEIVED PKG FROM SRV @ 192.168.1.4
    broadcasting in local subnet 
```

Server side:</br>
```
REGISTRATION  R;10.147.20.144;7788  from  10.147.20.144:55973
SRV: Number of regs: 1
SRV BROADCAST RECEIVED [192.168.1.7:4992]
        ==> Notifying remote [R;10.147.20.144;7788]
SRV BROADCAST RECEIVED [192.168.1.7:4992]
        ==> Notifying remote [R;10.147.20.144;7788]
```

Hint: ZeroTier works also with LTE routers and non-public IP adress


-------------- END OF standard installation -----------


If you need to redirect the traffic on clientside to anything other than 255.255.255.255 (default) you can apply the ``` LOCALBR``` argument e.g. ``` --LOCALBR=192.168.40.255``` I've discoverd that especially PfSense drops UDP packages directly to 255.255.255.255 - probably due to the fact it does not decide on which interface to send out the traffic

### Multi server (Client/Server/Server)

#### Server 1 VPN/Network site
```
./flex6k-discovery-util-go --SERVERIP=192.168.92.1 --SERVERPORT=7777
```

#### Server 2 VPN/Network site
```
./flex6k-discovery-util-go --SERVERIP=192.168.87.1 --SERVERPORT=7777
```

#### Client VPN/Network site
Simple add all server to the REMOTES argument.

```
./flex6k-discovery-util-go --REMOTES=192.168.92.1:7777;192.168.87.1:7777 --LOCALIFIP=192.168.40.1 --LOCALPORT=7788
```


### Multi site
If you host one or more radios on both sides of the tunnel or in different subnets and want to share accross networks you can run CLIENT & SERVER mode at the same time with the same process. 

#### Multi site node / relay loop

```
 ./flex6k-discovery-util-go --REMOTES=192.168.92.1:7777;REMOTES=192.168.87.1:7777 --LOCALIFIP=192.168.40.1 --LOCALPORT=7788 --SERVERIP=192.168.40.1 --SERVERPORT=7777
 ```



