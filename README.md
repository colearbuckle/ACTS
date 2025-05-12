# ACTS

ACTS (Analog Cellular Telecommunications System) is an open source project/protocol which aims to create a 1G/2G inspired cellular phone network. The system is limited to calls only, not unlike actual 1G back in the 1980s. Because of the frequencies and hardware, this system is unfortunately only legal with ham radio frequencies, requiring cell tower (base station) owners, and mobile phone users, to require a ham license.

Below is just an overview, and by no means instructions. I will likely produce some documentation and walkthroughs to intruct people on how to setup such a system. This is mainly just so that you can wrap your head around how this system operates, for a better understanding.

Key Words:

  - "BTS" Base Transceiver Station (cell tower)
  - "MS" Mobile Station (cell phone)
  - "MSC" Mobile Switching Center (routes calls between BTSs and MSs)

Before we start the explaination, the system uses DTMF tones for very basic signalling and control, because it is much simpler to interface with compared to something like FSK. FSK chips can be had fairly easily, but I have never messed with them before so I saw this as a safer option. Registeration happens with DTMF commands, calling happens with DTMF commands, and even the ring signal is sent via DTMF on the control channel.

There are a few options with this project. The MSC part is a piece of windows software that hosts a TCP server for the BTSs to connect to. This allows people to start their own cellular carrier, by hosting an MSC and then building your own BTSs for the network, or letting other peoples BTSs to be used on your network.

The actual BTS consists of an Ethernet to Serial converter box, which should be configured to connect to the IP and port of the desired MSC server. This box should then be connected to an Arduino Uno via an RS-232 to TTL converter. The Arduino should run the sketch provided. The Arduino will need to connect to an MT8870 DTMF module, for decoding the incoming DTMF commands, and the Arduino will also produce DTMF commands, so refer to the pinout provided.

That is roughly how the control side of the network works, but I may add an option for an artificial BTS that connects to the MSC, that acts as a PSTN gateway via something like SIP or VoIP.

The radio side is fairly simple in principle, but can get a bit confusing. All you need to know is that there are 3 groups of reuse frequencies, because if 3 cell towers were to overlap, they wouldn't interfere with each other. In theory, there could be any amount of voice channels, and also control channels, but for simplicity I will only have 1 control channel (3 if you count the fact that there are reuse frequencies). The control box (with the arduino etc) should ideally be enclosed in a box, with ports on it for power, serial, and audio in and out. The whole radio system could be simply built of DIY ham radio repeaters consisting of 2 Baofeng UV-5R radios connected together with VOX, per voice channel. The control channel is a bit different, and the arduino audio output should be connected to the audio input of a radio (e.g. Baofeng) with VOX enabled. Then there should be another radio with it's output connected to the DTMF decoder input.

The radio channels have different frequencies for transmit and receive, much like a ham radio repeater with an offset. This makes cell towers necessary, and reduces interference from other phones and base stations etc. Frequencies have not really been decided yet, because I don't really know what frequencies are appropriate for such a system.

You are probably wondering what phones I am going to use, which is a valid question becuase there aren't really any new off the shelf analog cell phones being produced anymore, and all of the original phones are collectors items and shouldn't be used. This is what influenced me to produce my own cellular analog protocol. There are around 3 main options for phones, mainly just a plain Baofeng UV-5R radio, a UV-5R with a ringer pack, and finally a custom built phone. The first option, just a UV-5R type device, is a good option because it is plug and play and fiarly simple, but it requires manual registration via typing the DTMF commands on the keypad, and it cannot ring becuase it doesn't have hardware for decoding DTMF commands. It's also a pain to make a call, becuase its not just as simple as dialling the desired phone number. To make a call, you have to type the whole command, including your phone number, before you can amke a call, and any error may result in an undesired outcome. The second option is a bit better, because it consists of a seperate box strapped to the radio, which inludes an arduino, DTMF decoder, and maybe a buzzer or speaker. This box connects to the radio's audio output, when a call isn't happening and the phone is just idle, and it detcts the DTMF ringer command for the phone which tells it to ring. This box could also register the phone with the network as well, but making calls wouldn't be possible becuase of the lack of a keypad etc. The third option is to build a phone from scratch, using an arduino, display (maybe those Nokia 5110 screens), keypad, and a DTMF decoder and finally an SA818 radio module. This would be a completely custom built phone that can register, receive, and make calls without any manual intervention. This is the ideal route, but may not be suitable for everyone.

Just a note: the cell tower control channels aren't always on (to stop damage to the UV-5Rs etc) and are VOX controlled, which could make custom phones displaying cell service signal quite difficult. Now, I may add a feature to the system so that every 5 seconds or so, the Arduino can transmit an inaudable tone that activates the radio and then the phone can read the RCCI of the transmittion, and display it on the screen until a new one is heard. This provides the illusion, as well as the working functionality, of a real signal meter.

Old README
<---------->

ACTS (Analog Cellular Telecommunications System) is an open source project/protocol which aims to create a 1G/2G inspired cellular phone network. Currently this is an SMS only system, but in the future the project may expand to voice calls as well, likely analog FM to keep the 1G inspiration.

Key Words:

  - "BTS" Base Transceiver Station (cell tower)
  - "MS" Mobile Station (cell phone)
  - "SMSC" Short Message Service Center (routes SMSs between MSs)

The way the project works is that users can download the ACTS-SMSC software, and can run it on windows. This software hosts a TCP server, which then needs to be connected to by some kind of client device which will be the BTS (Base Tranceiver Station). For my example, I have an Etherent to Serial converter which is set to connect to the IP address of the server, and then the RS232 side is connected to an RS-232 to TTL converter. The TTL side goes into an Arduino, which is connected to a CC1101 module. This is the main communications module that will be used for the radio communications between the phone (Mobile Station (MS)) and the BTS. The server can handle multiple simultaneous BTSs connected to it, which turns this system into your very own cellular carrier network, with as many cell towers (BTSs) as you want! 

The MS is built by using a CC1101 module combined with a battery, screen, keypad, microcontroller etc, and this makes a phone that supports the network. For my phone, I am using an Arduino Nano, 4x3 black keypad, Nokia 5110 screen (yes they still make them for hobbyist use, pretty cool!), and of course the CC1101 module. I have a piece of prototyping PCB board (perf board) that I am using as the main circuit board, with all of the components on it. Of course you can build your phone however you want, whether you want a Motorola DynaTAC 8000x inspired unit, or a flip phone formfactor, as long as the phone uses the correct commands and the CC1101, it should support the network. I am thinking of adding a cheap GSM module to the phone, but not for 2G communication. Instead it will only be used to read the SIM card phone number from the slot, using AT commands, and this means the phone can easily change numbers. Users can program their own SIMS, but if an unactivated SIM is put into the phone (for a real cell network) then that number can become their number on the network, when they register. The number will also be 100% unique, because it is a real phone number.

The commands are fairly simple:

  - "ACTS1" is a beacon signal that is sent every 5 seconds, and the 1 means the BTS is not allowed to transmit for the next 5 seconds, only the MS.

  - "ACTS2" is also a beacon signal, and when that is sent, for the next 5 seconds, only the BTS is allowed to transmit.

  - NOTE: ACTS1&2 are cycled every 5 seconds, and during a period where either cannot transmit, commands and messages are queued.

  - "NOTIFY22222222222FROM33333333333MSG(message contents)D" is for notifying the MS of an incoming SMS, where the first number is the recipient, and the second is the sender.

  - "REG22222222222D" is used to register the MS, and tell the SMSC exactly which BTS the MS is closest to, the 22222222222 is the number of the registering MS, and the D means the command is done and completed.

  - "SMS22222222222FROM33333333333MSG(message contents)D" is used to send an SMS, and the first phone number is the recipient number, the second is the number of the sender, and what is inside the brackets will be the SMS message contents, and finally D means the command is done and completed.

The last two commands are sent from the MS, and the rest are sent from the BTS.

The SMSC software will have a few .csv files in the same directory as the .exe file. The first (bts.csv) contains a list of 10 digit BTS IDs, that will be sent by the BTS within 20 seconds of first connecting to the SMSC, that authenticates it with the SMSC. If a valid ID is not sent within that time, the BTS will be kicked off the SMSC server, and will be forced to power cycle in order to attempt a connection again. If a connection between the BTS and SMSC is successful, the SMSC will send "OK" to the BTS and it can then know to start operations. If it wasn't successful, then it will send "NO" to the BTS. Once a connection is made though, the beacon signals, that are being received by the BTS should be forwarded to the radio module, and the NOTIFY command too. Any commands coming in on the radio side, if valid, should be sent over to the SMSC for proccessing.

For the SMSC, I also have an idea to create a built in Email to SMS gateway, which if I do correctly, could allow for interSMSC communication, if they all respect a dedicated phone number assignment system. The easiest way to do this is probably to just create a central file on this GitHub to have all the phone numbers, and then if someone wants to sign up for a number, they can just email me or something, and they can be added to the phone number list, which all of the SMSCs can choose to use if they want interSMSC communication. Another option is to just rely on everyone using a SIM card number. If they dont choose to have a GSM module, then they can just edit the Arduino code etc with their number, but they should get this number from the back of a brand new SIM card that they own and keep hold of, and since all SIM card phone numbers are unique, there is no way of interference. Of course this system is very insecure, which is why it is fun, so phone numbers can be easily copied and be impersonated, but theres not much we can do about that unfortunately, unless I want to add complex authentication, which I dont really want to do.

I have not completed the software or phone yet, so until I do there will be nothing else on this GitHub page.
