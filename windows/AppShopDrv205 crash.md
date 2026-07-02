# Windows BSOD due to 'AppShopDrv205.sys' driver 
-- Up to date as of July 2nd 2026 --

Relevant hardware config:

Old system
- GIGABYTE X570 AORUS ULTRA Rev 1.2 ([BIOS Version F40a](https://www.gigabyte.com/Motherboard/X570-AORUS-ULTRA-rev-11-12/support#Support-Bios))
- AMD Ryzen 9 5950X

New System
- ASRock Z690 Pro RS ([BIOS Version 21.01](https://www.asrock.com/MB/Intel/Z690%20Pro%20RS/index.asp#BIOS))
- Intel Core i9 14900K

## Crash conditions

Just switched across CPUs (in my case, from AMD to Intel) without uninstalling previous drivers

If you load into the login screen, everything is fine, however as soon as you try to go into Windows, you experience a 'Black Screen of Death' crash with:
```
Stop code: PAGE_FAULT_IN_NONPAGED_AREA (0x50)
What failed: AppShopDrv205.sys
```
PAGE_FAULT_IN_NONPAGED_AREA usually indicates a memory issue due to the grabbed page not being available, but this seems to be a driver bug too.

## My fix
1. Get into Command Prompt

    If Windows DOESN'T crash immediately when booting into the system:
    - Go to the Windows Recovery Environment
    Advanced Settings (F8) -> Command Prompt (6)

    If Windows DOES crash as soon as you even boot up:
    - On another device, create a [Windows Installation Media](https://www.microsoft.com/en-us/software-download/windows11) by going to Microsoft's website and follow the instructions (this should be in a USB drive or other external drive that can be plugged into the PC)

    Plug the drive into the PC and boot from that drive to get into the Windows Installation Environment

    Press Shift + F10 to enter Command Prompt

2. Find where AppShopDrv205.sys is by running these commands:
    ```
    // Only applies to you if you are using the installation media method
    diskpart
    list volume (find your main windows drive here)
    exit
    ```

    ```
    // applies for everyone
    C: (or whatever the letter of your windows drive is when you go into the command prompt/use diskpart)
    cd \ (goes into the top directory of your drive)
    dir /s /b AppShopDrv205.sys (searches every subdirectory for this file)
    ```

    Note: Should be located in C:\Windows\SysWOW64\Drivers\AppShopDrv205.sys, but always check to make sure

3. Once you find it, delete the file by running

    ```
    del /f /q "C:\Windows\SysWOW64\Drivers\AppShopDrv205.sys" (replace the path here with whatever the directory is)
    ```

4. We will now want to de-register it from regedit to make sure it doesn't boot up again. Type in
    ```
    regedit
    ```

    Go to Computer\HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\AppShopDrv205
    Double click on the 'Start' property and change the 'Value data' field from 3 to 4 (which will disable the driver)
    Press Ok, then Reboot