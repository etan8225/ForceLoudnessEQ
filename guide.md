# ForceLoudnessEQ Guide
#### First goal: Stop windows from auto-downloading drivers, as many effect audio enhancements.
If you want your audio enhancements as per usual, you'll need to change some windows settings first. This is for users who know how to update their drivers manually, and if you are uncertain, I would err on the side of caution and not do this. You _**may**_ be able to skip to the second section and have this guide still work, but it may not be as permanent as you would like or it may not work at all.
1. Start by pressing WindowsKey+R, and type in "sysdm.cpl", then hit enter.

![image](https://github.com/user-attachments/assets/d457e956-052f-4308-9d19-629a90430cc7)

Click on the "Hardware" tab, then click "Device Installation Settings"

![image](https://github.com/user-attachments/assets/205ee0dd-8cdb-44c1-8ade-4ed49a78f2b1)

And in the window that pops up, ensure "No" is selected, and hit "Save Changes". You can close the sysdm.cpl window.

![image](https://github.com/user-attachments/assets/5ca72e45-2e5c-4eef-8c00-f055199e1712)

2. Then, open run again with WindowsKey+R, and type in "gpedit.msc".

![image](https://github.com/user-attachments/assets/aef780b3-b62d-4cce-b4a8-773d974fe618)

On the right side, under "Computer Configuration", select "Adminitrative Templates" > "System" > "Device Installation" > "Device Installation Restrictions", and double click on "Prevent installation of devices not described by other policy settings". Enable the setting, click "Apply" then "OK".

![image](https://github.com/user-attachments/assets/0b5ca52b-92c4-47db-b3ce-837f2119c16e)

You can close out of the Group Policy window now.

3. In the start menu, (aka, hit the windows key) and type in "Check for updates", and hit enter.

![image](https://github.com/user-attachments/assets/08a4bd93-22a5-4350-8250-4876d49c2917)

In the bottom-middle of the window, you'll see "Advanced options", click it.

![image](https://github.com/user-attachments/assets/2951212c-fccc-4737-851f-3e3c8bf33291)

In the Advanced options window, disable "Receive updates for other Microsoft products when you update Windows".

![image](https://github.com/user-attachments/assets/6c8cceaa-d374-45e4-996b-ce9697392206)

You can close this window now.

4. Microsoft Store Settings

Open Microsoft Store by hitting the WindowsKey then type "Store". Open it, and in the top right, click your profile icon, then select settings.

![image](https://github.com/user-attachments/assets/f10ccc72-ea8b-427e-8b8d-154751e1dc63)

Ensure the top option, "App Updates", is disabled.

![image](https://github.com/user-attachments/assets/d4cb949d-8811-4cc0-a3e3-13770cc49694)

You can close the Microsoft Store now, but you are not done with it. You will need to run this powershell script to be able to fully disable Windows Store updates.

#### Second goal: Remove software that prevents audio effects from working.
