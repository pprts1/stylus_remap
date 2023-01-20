stylus remapping and redefining button behaviour 
'how-to' for remapping and redefining stylus buttons on linux

Hello everyone, this is a ‘how-to’ describing how I managed to successfully redefine one of my stylus' buttons on linux without
changing the button behaviour of my normal mouse. There may be a more elegant solution but this worked for me quite
nicely and I hope this can be reproduced on any linux distribution.

Problem: If you possess a drawing tablet+stylus of an unknown or lesser known brand then there is a chance that there is no
direct way in your linux distribution and/or environment to change the button behaviour of your stylus. Usually if the stylus is
recognized the system maps several buttons to the stylus automatically and recognises it as a normal mouse. Mapped Buttons
range from button ‘1 to X’, e.g. if the stylus is recognised as having 7 buttons then the button map looks like this: 

1 2 3 4 5 6 7

the actual buttons are 1 2 3 (1= left button, 2=mouse-wheel button, 3=right mouse button), buttons 4-7 are reserved to the mouse wheel turning. Since the stylus doesn’t come with a mouse wheel these buttons can be ignored. 

This was also the case for my ‘UC-Logic Technology Corp. TABLET 1060N’ stylus. In this approach I combined several programs
and wrote two small scripts and made them work together. The programs that I used are and that have to be installed for this
tutorial to work are (they are probably installed already in your system): xinput, xbindkeys, xdotool 

1) Using ‘xinput’ I remapped the buttons on my stylus, but instead of just changing the order of the buttons 

e.g. ‘1 2 3 4 5 6 7’ to ‘1 2 7 4 5 6 3’ (where button 3 and 7 switch), I introduced a new button (‘50’) that came neither defined for my stylus nor my mouse, and this button is then only defined for the stylus. So my stylus button map looked like this afterwards: 

1 2 50 4 5 6 7

therefore redefining 'button 3' on my stylus as 'button 50'.

2) The second thing I did with ‘xinput’ was mapping the stylus and the drawing pad to one of my displays specifically, so that if I move my stylus on my
drawing tablet from the left edge all the way to the right end my cursor moves on my prefered screen from the left end to the right end and does not reach any other monitor I may use.

$ xinput list 

this lists all devices recognised with their DEVICE_NAME and device ID.

using          xinput map-to-output pointer:“DEVICE_NAME” “SCREEN_NAME"            . In my case the command looks like this: 

$ xinput map-to-output pointer:“UC-Logic TABLET 1060N Pen Pen (0)” eDP-1-1

‘eDP-1-1’ is the name of my notebook screen which is got from: $ xrandr #this outputs monitor names apart from other things.
my primary monitor had the name ‘eDP-1-1’ and I used this in the xinput program. 
Then I remapped all my tablet devices to this particular screen, in my case three devices were recognised as ‘pointer’ in xinput:

xinput map-to-output pointer:“UC-Logic TABLET 1060N Pen Pen (0)” eDP-1-1 
xinput map-to-output pointer:“UC-Logic TABLET 1060N Consumer Control” eDP-1-1 
xinput map-to-output pointer:“UC-Logic TABLET 1060N Pad” eDP-1-1

Since this is undone every time you logout/restart your computer I wrote a script ‘xinput.sh’ which I manually start every time I
want to use my stylus button:

#! /bin/bash
xinput set-button-map pointer:"UC-Logic TABLET 1060N Pen Pen (0)" 1 2 50 4 5 6 7 
xinput map-to-output pointer:"UC-Logic TABLET 1060N Pen Pen (0)" eDP-1-1
xinput map-to-output pointer:"UC-Logic TABLET 1060N Consumer Control" eDP-1-1
xinput map-to-output pointer:"UC-Logic TABLET 1060N Pad" eDP-1-1

The first command sets the button map of my stylus and introduces the button ‘50’ instead of mouse button ‘3’ - you could also
instead of ‘50’ chose '100' or '150', just make sure your normal mouse doesn’t have this button mapped to it with:

xinput get-button-map pointer:"DEVICE_NAME_OF_YOUR_NORMAL_MOUSE"

my mouse has many buttons with a button map looking like this: ‘1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20’ - that’s why
I chose ‘50’ as the button for my stylus, I could also have used ‘21’ since it is not presend in the map of my normal mouse.

From the second to the last line I mapped all devices to one particular screen, ‘eDP-1-1’ using the device names instead of the
devide ID which may change if you unplug the usb and plug it in again. This way the device name is used which shouldnt
change (hopefully).

2) Then I introduced a custom behaviour for this button with xbindkeys to start a script. The lines I inserted into the
.xbindkeysrc file in my home directory (get’s created the first time you start xbindkeys and it asks you to define where to store
your xbindkeys configuration, in my case I typed: 

xbindkeys –defaults > ~/.xbindkeysrc

Then edit this .xbindkeysrc file in your home directory in the style demanded in the file itself: add the following lines:

## Start xdotool.sh script in $HOME/bin to startxdotool and emulate left-control + z
“xdotool.sh”
 b:50 + Release

3) The script launches 'xdotool' to return a keyboard combination (in this case: ‘left-control + z’ to undo the last thing I did in my
drawing application, e.g. removing a pen stroke on the canvas in GIMP).

the ‘xdotool.sh’ script has the following entries: 

#! /bin/bash
xdotool key Control_L+z

So every time the button on my stylus is recognized as button 50 and is RELEASED, the script xdotool.sh is started by xbindkeys
and xdotool returns Control_L+z.

You can of course change this key-combination to your liking (type ‘xdotool –help’ for
information), I just wanted to provide an example that is in my opinion useful for a drawing stylus.

To make things work both scripts have to be written and made executable with ‘chmod +x xinput.sh xdotool.sh’ and the
'xdotool.sh' script has to be copied to a $PATH directory of your currect user (in my case ~/bin). Make sure the 'xdotool.sh' file name is the same as
in the .xbindkeysrc configuration file!

The first script ‘xinput.sh’ MUST BE executed manually every time your stylus is replugged or you just logged in or restarted
your computer, however. But after that the button release event should be recognised and you can start working with your
drawing tablet with the new button function (in this case) left-control + z to “undo”.

I hope I could help some of you with this tutorial. With this approach you could also redefine keys on your drawing tablet itself
if it has buttons, but emulating a key combination with a stylus button was the problem I had to deal with so I took it as an
example here.

Cheers,
Philipp
