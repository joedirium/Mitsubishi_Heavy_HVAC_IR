# Mitsubishi_Heavy_HVAC_IR
description of the IR protocol of Mitsubishi Heavy air conditioners IR remote control protocol

I wanted to control my air conditioners with ESP8266 based Tasmota devices. Unfortunately I found that the built in support for Mitsubische HVAR IR does not work with my air conditioner. This is probaly because the supported protocol is for Mitsubishi Electric devices. Similar name, but different companies. 

So started reverse engeneering the PJA502A704AA remote control.<br/> 
![front picture](https://github.com/joedirium/Mitsubishi_Heavy_HVAC_IR/blob/master/Mitsubishi_remote_PJA502A704AA_front.jpg)
![back picture](https://github.com/joedirium/Mitsubishi_Heavy_HVAC_IR/blob/master/Mitsubishi_remote_PJA502A704AA_back.jpg)

This is what I learned:<br/>

carrier frequency: 36khz (not 38khz!!!!)
data transmitted is 64bit, 32 bit of data and 32 bit repeated as inverted bits

init sequence:
	pulse 6000
	gap 7400
	500 pulse
	1480/3460 gap for 0/1
out sequence
	7400 gap
	500 pulse

bit  1-12: 010100000000
bit 13-14: fan speed 00 low,10,01 high
bit 15   : air flow swing, 1=on
bit 16   : 0
bit 17-20: temp 0000,1000-1111
bit 21-23: operation modes 000=auto, 010=cool, 001=heat, 100=dry, 110=fan
bit 24   : 0 für off, sonst 1
bit 25-28: 0000
bit 29-30: air flow up=auto, 11=down
bit 31-32: 10
bit 33-64: inversion of 1-32

A bis issue was to find out that the remote is not using the usual 38khz carrier, but 26khz. I used a small and cheap digitial oszilloscope DS212 to figure this out after all my tries based on 38khz failed. 

So an example to turn of a running air condition with Tasmota is 
irsend 36,6000,7500,500,1500,500,3500,500,1500,500,3500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,3500,500,1500,500,1500,500,3500,500,1500,500,3500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,1500,500,3500,500,1500,500,3500,500,1500,500,3500,500,1500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,1500,500,3500,500,3500,500,1500,500,3500,500,1500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,3500,500,1500,500,3500,500,7500,500

As you can see, there is a special end mark in this raw sequence. So normal bit coding with tasmota will probably not work.
