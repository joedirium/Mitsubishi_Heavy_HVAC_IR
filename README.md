# Mitsubishi_Heavy_HVAC_IR
description of the IR protocol of Mitsubishi Heavy air conditioners IR remote control protocol

I wanted to control my air conditioners with ESP8266 based Tasmota devices. Unfortunately I found that the built in support for Mitsubische HVAR IR does not work with my air conditioner. This is probaly because the supported protocol is for Mitsubishi Electric devices. Similar name, but different companies. 

So started reverse engeneering the PJA502A704AA remote control.<br/> 
![front picture](https://github.com/joedirium/Mitsubishi_Heavy_HVAC_IR/blob/master/Mitsubishi_remote_PJA502A704AA_front.jpg)
![back picture](https://github.com/joedirium/Mitsubishi_Heavy_HVAC_IR/blob/master/Mitsubishi_remote_PJA502A704AA_back.jpg)

The following information is about my personal experience without any liability or guaranties and may be faulty.
Use this information at you own risk only.

This is what I learned:<br/>

carrier frequency: 36khz (not 38khz!!!!)
data transmitted is 64bit, 32 bit of data and 32 bit repeated as inverted bits

init sequence:<br/>
-pulse 6000<br/>
-gap 7500<br/>
the data bits
-pulse 500<br/>
-gap 1500/3500 for bit 0/1<br/>
out sequence<br/>
-gap 7500<br/>
-pulse 500<br/>

bit  1-12: 010100000000<br/>
bit 13-14: fan speed 00=lowest,10=medium, 01=highest, first bit is lsb<br/>
bit 15   : air flow swing, 1=on<br/>
bit 16   : 0<br/>
bit 17-20: temp 0000=17°C,1000-1111=30°C, first bit is lsb<br/>
bit 21-23: operation modes 000=auto, 010=cool, 001=heat, 100=dry, 110=fan<br/>
bit 24   : 0 means "power off", 1 means "power on"<br/>
bit 25-28: 0000<br/>
bit 29-30: air flow direction 00=auto, 10=up, 11=down<br/>
bit 31-32: 10<br/>
bit 33-64: inversion of 1-32<br/>

A big issue was to find out that the remote is not using the usual 38khz carrier, but 36khz. I used a small and cheap digitial oszilloscope DS212 to figure this out after all my tries based on 38khz failed. 

So an example to turn of a running air condition with Tasmota is:<br/> 
```irsend 36,6000,7500,500,1500,500,3500,500,1500,500,3500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,3500,500,1500,500,1500,500,3500,500,1500,500,3500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,3500,500,1500,500,3500,500,1500,500,3500,500,1500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,1500,500,3500,500,3500,500,1500,500,3500,500,1500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,1500,500,3500,500,7500,500```

As you can see, there is a special end mark in this raw sequence. So normal bit coding with tasmota will probably not work.
