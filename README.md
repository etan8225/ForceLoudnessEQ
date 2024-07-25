# ForceLoudnessEQ
MAJOR NOTE: I may update this later, but in the meantime, go to [this youtube video](https://www.youtube.com/watch?v=WmdMPfWAci0) to get a more fully updated version of this guide. It has some important notes on things you must disable / delete to stop your windows from forcefully reverting your settings.

Force Loudness EQ for outputs that support enhancements - note, this will disable all software enhancements made by the headset (like DTS:X)

After having my own problems with enabling Loudness EQ, I decided to make this repo for those trying to implement it themselves. But, I figure I should start out with the basics.

# Why are enhancements auto-enabled with some audio drivers and not others?
Certain audio drivers may reference their own sAPO's (system effect APOs). This allows headphone manufacturers to implement, for example, DTS:X audio effects. These effects are generally kept in your headphone drivers' registry folder, located at

`Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\MMDevices\Audio\Render\{XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX}\FxProperties`

with your headphones' id in place of {XXX...}. If you don't know your headphones ID, go to `~\Audio\Render`, and open each key, looking in the "Properties" folder for indications that you are looking at your headphones' key (for example, my headphones had the strings "HyperX Cloud II Wireless" and "DTS Audio Effects for HyperX Relay headset" in ~\Properties and ~\FxProperties, respectively). There may be multiple entries for a single device as well, so look through ALL of the keys regardless if you "already found the folder". Keep the registry & those individual "folders" open in the background.

# I got the ID(s) for my headset, what do I do now?
There are 2 ways you can advace from here. Either, 
1. Look through the Properties key, searching for readable text, and find a file with the ending .inf. For example, my headset was set up with "oem61.inf" on windows 11, and "wdma_usb.inf" on windows 10.
2. If you don't want to do that, go to device manager -> right click on your headset and go to properties-> go to events, and look for the inf files that are used there. If there are more than 1, ignore the default windows infs (wdma_usb, wdmaudio, etc) and pay attention to OEM infs instead.

Keep this inf in mind when you proceed to the next step.

# Got the inf and the ID(s), what's next?
Go to `C:\Windows\INF`, and search for the inf you found. Open the inf in Notepad, and use "Find" to search for `{D04E05A6-594B-4fb6-A80D-01AF5EED7D1D}` (without matching the case). You should find at least 2 of these strings in this inf, with seperate numbers appended onto the end (for example, `{D04...D1D},5 & {D04...D1D},6` for myself on windows 10, and `{D04...D1D},13 & {D04...D1D},14` for myself on windows 11). Note which string is equal to `StreamEffectClsid` and `ModeEffectClsid` (and also possibly `EndpointEffectClsid`). It may look something like this:
```
PKEY_CompositeFX_StreamEffectClsid   = "{D04E05A6-594B-4fb6-A80D-01AF5EED7D1D},13"
PKEY_CompositeFX_ModeEffectClsid     = "{D04E05A6-594B-4fb6-A80D-01AF5EED7D1D},14"
OR
PKEY_FX_StreamEffectClsid  = "{D04E05A6-594B-4fb6-A80D-01AF5EED7D1D},5"
PKEY_FX_ModeEffectClsid    = "{D04E05A6-594B-4fb6-A80D-01AF5EED7D1D},6"
OR possibly something else
```
Go back to your registry, and set your `StreamEffectClsid` to `{62dc1a93-ae24-464c-a43e-452f824c4250}`, and set your `ModeEffectClsid` to `{637c490d-eee3-4c0a-973f-371958802da2}`. If you do not see the Effect tab at all (you can check quickly with Win+R, "control mmsys.cpl sounds", then right click your headphones and check properties), you can manually add the effect tab by adding the string 

`{D04E05A6-594B-4fb6-A80D-01AF5EED7D1D},3` and setting the value to `{5860E1C5-F95C-4a7a-8EC8-8AEF24F379A1}`.

These CLSID's/strings will essentially force your headphones' driver into using the default windows audio enhancements rather than their own.
Now, restart and check if the effects are there. They should be.
# I did that, but the effects didn't show up/aren't actually working!
Some audio drivers "force" their effects to be used by overwriting the registry on startup. If you have this problem, you have to manually change [this githubs](https://github.com/Falcosc/enable-loudness-equalisation/blob/main/EnableLoudness.ps1)' PS1 file to match your computer's needs (the file assumes your computer uses `{D04...D1D},1 & {D04...D1D},2` set to `{62dc1a93-ae24-464c-a43e-452f824c4250}` and `{637c490d-eee3-4c0a-973f-371958802da2}` respectively, but if you found different numbers appended to the end of `{D04E05A6-594B-4fb6-A80D-01AF5EED7D1D}` you MUST change those to match your computer. For example, if your oem inf showed
```
PKEY_FX_StreamEffectClsid = {D04E05A6-594B-4fb6-A80D-01AF5EED7D1D},x 
PKEY_FX_ModeEffectClsid   = {D04E05A6-594B-4fb6-A80D-01AF5EED7D1D},y
```
you have to change the ps1 file to show
```
"{d04e05a6-594b-4fb6-a80d-01af5eed7d1d},x"="{62dc1a93-ae24-464c-a43e-452f824c4250}" ;PreMixEffectClsid activates effects
"{d04e05a6-594b-4fb6-a80d-01af5eed7d1d},y"="{637c490d-eee3-4c0a-973f-371958802da2}" ;PostMixEffectClsid activates effects
```
Then save it, and go through the steps shown on that github to set it to auto-run on launch / when you wake the computer from sleep. This should FORCE your computer to use windows' audio effects, although if it does not, make sure you applied these changes to **ALL** of your headphones "folders" in the registry and then restart. If it still doesn't work, there is one more possible solution (**untested**).If your computer showed an "EndpointEffectClsid" in the oem inf, you can try to delete that string in the registry to see if your computer starts using the proper effects. This is because, in short, certain effects overwrite other effects. 

Technically, this is because SFX, MFX and EFX effects overwrite GFX and LFX effects. Windows audio effects are classified as GFX and LFX effects, so they could be overwritten by any other audio effect. Fair warning though, I haven't been able to test this 'fix' at all, as none of my headsets support EFX. If something does go wrong after a restart, all you would have to do is re-install the driver for that headset (Win+R, "control mmsys.cpl sounds", right click your headset, properties, and reinstall the driver). Reinstalling the driver would also change the headsets' key in the registry so you'll have to look for it again. The headset will also work normally after the reinstall, with the default "custom" effects (NOT windows enhancements).

Referenced sources:

[Falcosc's powershell script](https://github.com/Falcosc/enable-loudness-equalisation)

Unreferenced sources:

[dechamp's windows APO guide](https://github.com/dechamps/APO) ; wdma_usb.inf (in C:\Windows\INF) ; oem61.inf (hyperX's OEM INF)
