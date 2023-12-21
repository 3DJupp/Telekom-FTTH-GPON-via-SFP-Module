# TFTTH-GPON-via-SFP-Module
This project is used to describe how a dedicated SFP+ can be used in order to get rid of another Router, Media converter. Might apply to other vendors aswell.
At first you have to get a compatible Modem which supports GPON as well as the SFP+ format.
My advise is to buy the fiber modem intended for business customers -> [Digitalisierungsbox Glasfasermodem](https://geschaeftskunden.telekom.de/internet-dsl/produkt/digitalisierungsbox-glasfasermodem-kaufen). At the time of writing this summary it'll cost 40€ net (VAT is not included, so roughly 60€ including shipping)

Next step will be to get some data related to your contract, such as the Installation-Password (PLOAM). Just a 10Digit/Letter combination they should give you. (Technician should be able to retrieve it)

![image](https://github.com/3DJupp/Telekom-FTTH-GPON-via-SFP-Module/assets/8407566/9db23155-5920-42de-9680-2bd091213414)

You also need some details for the dial-in/PPPoE connection which you should be able to retrieve in the customer portal.
Dummy data:
Zugangsnummer: TTTTTTTTTT (Access Number)
Kennwort:		PPPPPPPP (Password)
Anschlusskennung:	AAAAAAAAAAA (Line-ID)
Mitbenutzernummer:	MMMM (Co-User-Number, typically 0001)

PPPoE will be generated from those Numbers:
AAAAAAAAAAATTTTTTTTTTMMMM@t-online.de
Line-ID+Access Number+Co-User-Number@t-online.de
Password: PPPPPPPP (you'll have to enter yours)

Set an IP for the Config the SFP Modem. (192.168.100.1/service.html)
If using and UDM-Pro, just do the following:
#UDM Pro Interface Config (WAN2/SFP with UF Instant)
ifconfig eth9:2 192.168.100.123 netmask 255.255.255.0 up
#verify
ping 192.168.100.1
ping 192.168.100.123

Now You should be able to connect to the service page in order to enter the Installation Password.
The URL is: 192.168.100.1/service.html

![sercomm_service_page](https://github.com/3DJupp/Telekom-FTTH-GPON-via-SFP-Module/assets/8407566/722d9a5b-c4ba-4993-ad68-dda723e22953)
Enter the desired PLOAM and hit "save".

Now you might remove the temporary interface on your UDM/Windows Network settings etc. 
ifconfig eth9:2 down
Or restart the UDN :)

Now its time to add the Interface in the UDM-Pro (Web console.)
Just add the PPPoE Details gathered above. For Telekom customers the user should look like:
AAAAAAAAAAATTTTTTTTTTMMMM@t-online.de
Password should be 8 Digits:
P4ssw0rd
VLAN: Please use VLAN 7.

So if you are using an UDM-SE/UDM-Pro your config should look like:

<img width="511" alt="image" src="https://github.com/3DJupp/Telekom-FTTH-GPON-via-SFP-Module/assets/8407566/74b422a8-fc25-4a25-a333-7f2f27664c9c">

*But* we are not done here. The modem has to be "reprovisioned" which can be done via Hotline or technician only. (They need to know the new MAC of your Modem)
If that's done, your setup should look like this (or similiar):

![image](https://github.com/3DJupp/Telekom-FTTH-GPON-via-SFP-Module/assets/8407566/766431e6-cbc9-410e-bafa-49ca23a93d88)
![image](https://github.com/3DJupp/Telekom-FTTH-GPON-via-SFP-Module/assets/8407566/c427300d-fdbd-42dc-8658-094031f4361c)

Fiber rocks!
This is by far the most reliable way. Yes, the reprovision sucks, but thats a regulatory Telekom-Thing.
