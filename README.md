# hide-systems
A way to hide and unhide systems from within emulation station.
created by wellslight

This is a walkthrough on how to create a functional "hide system" button in the emulationstation front end and a "restore system" in the retropie menu. The walkthrough will show you how to do this with one system, NES. To make this with other systems simply edit the places that you see "nes" (examples: ~/roms/nes , restorenes.sh). I recommend working through this with one system completely to make sure everything is working, so you don't have to edit several files if you find an error.


Part 1: Hiding the System
	
Step 1. copy /etc/emulationstation to /opt/retropie/configs/all/emulationstation
    all future edits to es_systems.cfg will be done in /opt/retropie/configs/all/emulationstation

Step 2. Go into /opt/retropie/configs/all/emulationstation/es_systems.cfg and add ".sh .SH" to the extensions, save and exit. 

Each system is listed and looks like this:

	<system>
    <name>nes</name>
    <fullname>Nintendo Entertainment System</fullname>
    <path>/home/pi/RetroPie/roms/nes</path>
    <extension>.nes .zip .NES .ZIP</extension>
    <command>/opt/retropie/supplementary/runcommand/runcommand.sh 0 _SYS_ nes %ROM%</command>
    <platform>nes</platform>
    <theme>nes</theme>
	</system>

We want to add a shell script in the gamelist that allows us to hide the system. For the script to show up in the list of games we need to add ".sh .SH" to the extension list, so it looks like this: <extension>.nes .zip .NES .ZIP .sh .SH</extension>

Step 3. Create a hiddensystems folder (sudo mkdir) in roms: /home/pi/RetroPie/roms/hiddensystems

Step 4. Create a restoresystems folder in roms: /home/pi/RetroPie/roms/restoresystems

Step 5. Create a systems folder in retropiemenu: /home/pi/RetroPie/retropiemenu/systems

Step 6. Now create a shell script (sudo nano) in the system's folder: /home/pi/RetroPie/roms/nes/hidesystem.sh
(alternatively you could make a folder ".~/RetroPie/roms/nes/xhidesystem/hidesystem.sh" to store the shell script in so that the hide system option is listed at the bottom of the roms and you have to enter the folder before being able to push the hide button.

Copy and paste this script as your hidesystem.sh:

			#!/bin/bash
			sudo mv /home/pi/RetroPie/roms/nes /home/pi/RetroPie/roms/hiddensystems/
			sudo mv /home/pi/RetroPie/roms/restoresystems/restorenes.sh /home/pi/RetroPie/retropiemenu/systems/restorenes.sh
			sudo reboot
			
This script moves the nes folder to a new "hiddensystems" folder, removing it from visibility in the emulationstation front end.
It also moves a shell script that we are storing in a "restoresystems" folder over to the restropie menu (we will make this soon and it allows us to move the nes folder back so the system is restored). It then reboots the pi so that the hide takes place immediately.

Step 7. In the terminal give the script permission: 

			cd /home/pi/RetroPie/roms/nes
			sudo chmod a+x ./hidesystem.sh

Now this shell script should work. To test it go into your terminal and type "/home/pi/RetroPie/roms/nes/hidesystem.sh" . You should see the nes folder leave the roms folder and enter the hiddensystems folder. Remember, it reboots your pi so save anything you don't want to lose.

I went in and added a picture/renamed the script in the system menu by going into emulationstation, scroll over the "hidesystem" option in your roms list (or in the folder if you made one), then press "Select" and choose the edit data option where you can rename it and add a path for an image.



Part 2: Restoring the System

Step 8. Create a shell script (sudo nano) in the restoresystems folder: /home/pi/RetroPie/roms/restoresystems/restorenes.sh

This is a location to store all of the shell scripts that restore hidden systems, you don't want them appearing in the retropie menu until you've actually hidden the system. This is why this shell script contains the system name in the title (nes), because all the systems restore scripts will be stored in this file.
		
Copy and paste this script as your restorenes.sh:

			#!/bin/bash
			sudo mv /home/pi/RetroPie/roms/hiddensystems/nes /home/pi/RetroPie/roms/nes
			sudo mv /home/pi/RetroPie/retropiemenu/systems/restorenes.sh /home/pi/RetroPie/roms/restoresystem/restorenes.sh
			sudo reboot
			
This script moves the nes folder from the hiddensystems folder back into the roms folder where emulationstation can detect it again and show the system in the front end. It also moves itself (the restore shell script) back into the folder storing all of the restore shell scripts so that it is no longer an option in the retropie menu. It then reboots the pi so the changes take effect immediately.

Step 9. In the terminal give the script permission:

			cd /home/pi/RetroPie/roms/restoresystems
			sudo chmod a+x ./restorenes.sh
	
Now this shell script should work. To test it go into your terminal and type "home/pi/RetroPie/roms/restoresystems/restorenes.sh" . It should move your nes folder back into the roms folder (so it would have to be there in the first place - execute your hide system script before testing this one, it should bring everything back to normal).

Again, you can add an image and rename it if you want. I renamed all of my with an "x" at the beginning so they show up at the bottom instead of in the middle of my games.


OPTIONAL (just for looks):
If you want to make it so your retropie menu systems folder "restoreXXX" files have pictures you can go to
/opt/retropie/configs/all/emulationstation/gamelists/retropie/gamelist.xml and add the following for each "restoreXXX.sh" script you make.

	<game>
		<path>./systems/restorenes.sh</path>
		<name>Restore Nintendo (NES)</name>
		<desc>Restore hidden system</desc>
		<image>~/RetroPie/roms/art/restore.png</image>
		<rating>0</rating>
		<playcount>3</playcount>
		<lastplayed>20170223T194048</lastplayed>
	</game>
	
Create an "art" folder: "home/pi/RetroPie/roms/art" and store your art here.

You could also alternatively have the "hide systems" option in the retropie menu as well. This would condense hiding the system and restoring the system all to the retropie menu. To do this you would simply change your hide shell script to hidesystemnes.sh (specify the system since all will be stored here), and create a folder in /home/pi/RetroPie/ for hide systems, then store those scripts here.

You can see the process here: https://www.reddit.com/r/RetroPie/comments/5w6k9e/customized_retropie_emulationstation_to_be/
