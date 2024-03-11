# TFTTH-GPON-via-SFP-Module
## Modem Selection
First, you need to get a compatible modem that supports both GPON and the SFP+ format. My advice is to buy the fiber modem intended for business customers -> Digitalisierungsbox Glasfasermodem. At the time of writing this summary, it costs 40€ net (VAT not included, so roughly 60€ including shipping).

[Digitalisierungsbox Glasfasermodem](https://geschaeftskunden.telekom.de/internet-dsl/produkt/digitalisierungsbox-glasfasermodem-kaufen)

## Contract Data

### PLOAM
The next step is to get some data related to your contract, since "Newer than FTTH 1.7=FTTH GGS" the Installation Password (PLOAM) is not needed anymore. If you still need it, ask for the PLOAM/Installationskennung in German.
If you're not sure if you're GGS or still on 1.7, check the remarks in the [Telekom Forum](https://telekomhilft.telekom.de/t5/Festnetz-Internet/Woran-kann-man-einen-FTTH-1-7-von-einem-FTTH-Gigabit/ta-p/5429973)

### PPPoE Connection

You also need some details for the dial-in/PPPoE connection which you should be able to retrieve from the customer portal.

Dummy data:
- Access Number: TTTTTTTTTT
- Password: PPPPPPPP
- Line-ID: AAAAAAAAAAA
- Co-User-Number: MMMM (typically 0001)

The PPPoE will be generated from these numbers:
```
AAAAAAAAAAATTTTTTTTTTMMMM@t-online.de
Line-ID+Access Number+Co-User-Number@t-online.de
Password: PPPPPPPP (you'll have to enter yours)
```

## SFP Modem Configuration
Set an IP within the subnet for the configuration of the SFP+ Module or ONT.<br>Service IP for the Sercomm Modems is **192.168.100.1/24**<br>If you use Zyxel / PMG3000-D20B it is **10.10.1.1/24**

If using an UDM-Pro, just do the following:
```bash
# UDM Pro Interface Config (WAN2/SFP with PMG3000-D20B)
ifconfig eth9:2 10.10.1.2 netmask 255.255.255.0 up
# verify
ping 10.10.1.1
ping 10.10.1.2
```
Now you should be able to connect to the service page to enter the Installation Password.
### Sercomm
The URL for Sercomm is: [http://192.168.100.1/service.html](http://192.168.100.1/service.html)
![sercomm_service_page](https://github.com/3DJupp/Telekom-FTTH-GPON-via-SFP-Module/assets/8407566/722d9a5b-c4ba-4993-ad68-dda723e22953)
Enter the desired PLOAM and hit “save”.<br>
### Zyxel
The URL for Zyxel PMG3000-D20B is: [http://10.10.1.1](http://10.10.1.1)<br>Username: admin<br>Password: admin
#### SSH Config
In case you want to change things, that might be done via SSH.
##### Change serial
The first option is not permanent/will be reverted:
```bash
sn serialnumber
```
Thats permanent / reboot-safe:
```bash
sn set serialnumber
```
serialnumber could be anything,<br>
like
- **SCOM00012345** -> Sercomm
- **ZYWNA1234567** -> Zyxel

It's useful in case you want to have multiple valid SFPs and/or ONTs for testing. More information on the Vendor prefix at.: [hack-gpon.org](https://hack-gpon.org/vendor)
##### Troubleshooting
If the serialnumber was already accepted, you can check the status via "onu ploamsg":
```bash
admin@SFP:~# onu ploamsg
errorcode=0 curr_state=5
```
- O1 Initial: the OLT sends a message to the ONU to start the ONU, and the ONU enters the standby state;
- O2 Standby: After receiving the message, the ONU extracts the delimiter value, power level, and pre-allocated compensation delay from the message, and adjusts its configurations accordingly, to support subsequent information exchanges.
- O3 Serial number: The OLT sends a serial number (SN) request to the ONU. The ONU sends its SN to the OLT. After receiving the ONU’s SN, the OLT allocates a temporary ONU-ID to the ONU.
- O4 Ranging: The OLT sends a ranging request to the ONU. After receiving the ranging request from the OLT, the ONU responds with a message carrying its SN and ONU-ID. The OLT calculates the compensation delay and sends it to the ONU in a message. After receiving the message, the ONU sets the compensation delay accordingly.
- O5 Operation: The OLT sends a password request to the ONU. The ONU returns a password to the OLT.
- O6 Intermittent LODS state.
- O7 Emergency Stop state.

Source: [hack-gpon.org](https://hack-gpon.org/gpon-auth)

##### Link speed to 2.5Gbit / HGSMII (PMG3000-D20B)
> [!WARNING]  
> Please check if your modem does support HSGMII (Proprietary link speeds for SFP+ Modules). Ubiquiti does not for most of their products.<br>
Mirkotik does, so i execute those commands and change the link-Speed to 2500-Base-X in RouterOS/Mikrotik config.
```bash
ZYXEL# hal
Hal# set speed 2.5g mode full
```

Now you might remove the temporary interface on your UDM/Windows Network settings etc.
```bash
ifconfig eth9:2 down
```
Or restart the UDM :)

## Adding the Interface
Now its time to add the Interface in the UDM-Pro (Web console.)
Just add the PPPoE Details gathered above. For Telekom customers the user should look like:
```
Username: AAAAAAAAAAATTTTTTTTTTMMMM@t-online.de
Password: P4ssw0rd (should be 8 Digits)
VLAN: 7
```

So if you are using an UDM-SE/UDM-Pro your config should look like:

<img width="511" alt="image" src="https://github.com/3DJupp/Telekom-FTTH-GPON-via-SFP-Module/assets/8407566/74b422a8-fc25-4a25-a333-7f2f27664c9c">

## Reprovision
**We are not yet done here.** The modem has to be "reprovisioned" which can be done via Hotline, technician, or shall happen periodically.
They system needs to know the new Serial or MAC of your Modem etc. If that's done, your setup should look like this (or similiar):

<p float="left">
  <img src="https://github.com/3DJupp/Telekom-FTTH-GPON-via-SFP-Module/assets/8407566/766431e6-cbc9-410e-bafa-49ca23a93d88" width="100" />
  <img src="https://github.com/3DJupp/Telekom-FTTH-GPON-via-SFP-Module/assets/8407566/c427300d-fdbd-42dc-8658-094031f4361c" width="100" /> 
</p>

This is by far the most reliable way. Yes, the reprovision sucks, but thats a regulatory Telekom-Thing.
$${\color{magenta}Fiber} \space {\color{magenta}rocks!}$$
