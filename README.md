FORKED FROM https://github.com/dajusc/Linux-Mouse-Keybinds

# NOTE! To get this to work with Logitech g300s
By default, most of the extra buttons on the the g300s don't actually trigger a mouse button event.  They assume you're going to bind them to those or keypresses or whatever using their software (which you can do).  If you're on Linux you can't use the Logitech software, so you can use https://github.com/krayon/ratslap to do that.  I configured my mouse as follows, noting that I didn't bother configuring all of them (I can't reach them all).

```
$ sudo ./ratslap -mf4 --g5 button6 --g4 button7 --g6 button9 --g7 button8 --select f4
RatSlap v0.3.2 (BUILT: 2020-05-25 13:48:43+1000)
Copyright (C) 2016-2020 Todd Harbour
Linux configuration tool for Logitech mice (currently only G300/G300S)
https://gitlab.com/krayon/ratslap/
Found Logitech G300s (046d:c246) @ 0x558c79155cd0
Detaching kernel driver...
Modifying Mode: F4
    Setting button 5: button6
    Setting button 4: button7
    Setting button 6: button9
    Setting button 7: button8
Mode Selection Specified: F4
Saving Mode: F4
Selecting Mode: F4
Attaching kernel driver...
```
From there the rest of the Linux-Mouse-Keybinds doco should work ok and that's what follows here.

# Linux-Mouse-Keybinds
Configurable mouse button keybinds for linux. Works for Wine/Proton apps. Features automatic profiles.

## Overview
Binding keyboard keys to the buttons of gaming mouse is essential for some users (like me).
For Windows and Mac the vendors offer configuration software, e.g. "*RAZER Synapse*", "*ROCCAT Swarm*" or "*Logitech Gaming Software*", while they do not offer a linux version.
For linux there are tools like **xbindkeys** and **imwheelrc** which work nice for X-applications, but do unfortunately stop to work as soon as a Wine or Proton (Steam Play) game is started.
[Piper](https://github.com/libratbag/piper) is a very cool project for configuring gaming mouse, but its keybinding functionality just didn't work out for me (e.g. ESC-key can not be assigned).

So I wrote this lightweight keybinder-script in **Python** (no GUI), based on the **evdev** module and **xdotool**.
No installation is required and it works in Wine or Proton.
Because a window name or PID can be given in the binding configuration, the script features an fully automatic switching of the keybindings for as many different games as you want.
Also callback functions can be bound to on/off-focus-events, which is usefull for implementing automatic enabling/disabling of mouse accelleration (e.g. via xinput, not part of Linux-Mouse-Keybinds).

## Usage
Open "*linuxmousekeybinds.py*" in a text editor, scroll to the bottom and configure to your needs (help see beow).
Then start a terminal (e.g. *bash*) navigate to the scripts directory and type:
```
$> python3 ./linuxmousekeybinds.py
```
You may now start your game, e.g via Wine or Proton (Steam Play), and leave the script running in the background.
The keybinding stops working as soon as the script exits (ctrl+C) or the terminal is closed.

## Dependencies and Preconditions
Your linux user needs to have access to evdev, so e.g. has to be menber of the input group on Debian based systems.
Python (2 or 3) and xdotool need to be installed.

## Warnings
- The script does **not unbind** any differently applied bindings or native functions of the mouse buttons. It basically just applies the keystrokes *on top* of the already existing functionality of the buttons.
- It seems possible that **anti cheat** engines for multiplayer games may categorize the actions performed by this script as cheating. So use it in singleplayer only or don't blame me if you get into trouble. ;)

## Configuration tips and examples
Below you can see a configuration example.
If you misconfigure *linuxmousekeybinds* it will give you usefull tips about the allowed settings.
```python
lmkb = linuxmousekeybinds("Logitech G500s Laser Gaming Mouse")

lmkb.bind_key_to_button("Tomb Raider", "BTN_EXTRA",   "3")       # thumb button forward
lmkb.bind_key_to_button("Tomb Raider", "BTN_FORWARD", "c")       # thumb button middle
lmkb.bind_key_to_button("Tomb Raider", "BTN_SIDE",    "Escape")  # thumb button backward
lmkb.bind_key_to_button("Tomb Raider", "REL_HWHEEL+", "r")       # wheel sideways left
lmkb.bind_key_to_button("Tomb Raider", "REL_HWHEEL-", "v")       # wheel sideways right

lmkb.bind_key_to_button(7154, "BTN_SIDE", "3")  # binding by PID instead of window-name
lmkb.bind_key_to_button(None, "BTN_SIDE", "1")  # default binding for any other window

lmkb.bind_key_to_button("Doom", "BTN_EXTRA", ["1", 500, "2"])  # Macro: "1", 500ms delay, "2"
lmkb.bind_key_to_button("Doom", "BTN_SIDE",  ["3-", 50, "3+"]) # Macro: "3"-keydown, 50ms delay, "3"-keyup

def cb1():
    print("Tomb Raider got focus!")
def cb2():
    print("Tomb Raider lost focus!")
lmkb.set_callback_focus_on( "Tomb Raider", cb1) # cb1 will be executed on Tomb Raider getting focus
lmkb.set_callback_focus_off("Tomb Raider", cb2) # cb2 will be executed on Tomb Raider loosing focus

lmkb.run()
while lmkb.is_running():
    time.sleep(.1)
```
