# "Windows 10 installation speedrun guide with OOBE skip"
> "took me hours to write this"

- toc:true- branch: master
- badges: true
- comments: true
- author: Hamel Husain & Jeremy Howard
- categories: [fastpages, jupyter]

## Windows 10 installation speedrun 

"No gpt covertion" because I don't think virtualbox supports gpt installation of windows 10

Open cmd.exe in installation mode: <kbd>Shift</kbd> + <kbd>F10</kbd>

### create partition and drives with Diskpart.exe | `diskpart`

```batch
sel disk 0
create part primary size=500
format fs=fat32 quick
assign letter w

create part primary
format fs=ntfs quick
assign letter c
```

Shorter form:
```batch
sel disk 0
cre par pri size=500
for fs=fat32 quick
ass let c

cre par
for quick
ass let w
```

### apply install.esd to C dirve using dism.exe | `dism`

`cd /d d:\sources`

Note: make sure `d:\sources` contains install.esd or install.wim

```batch
dism /apply-image /imagefile:install.esd /index:1 /applydir:C:\
```

### OOBE setup skip

After dism finished extracting registry keys in `C:\Windows\system32\config`:

Open registry with cmd `regedit.exe` or `regedit`

Click on `HKEY_LOCAL_MACHINE` key and go to the menu bar and select file > Load Hive

Then select `C:\Windows\system32\config\SYSTEM`, click open (loading the keys may take a while)

Name it anything you want, but I will name it `sys`.

go to Computer\HKEY_LOCAL_MACHINE\sys\Setup

on the right of the Registry editor: double click on or press enter after selecting `CmdLine` in the name column > type in `cmd.exe`

then select `OOBEInProgress` key > enter `0`

Click on `HKEY_LOCAL_MACHINE` key and go to the menu bar and select file > Load Hive

Then select `C:\Windows\system32\config\SOFTWARE`, click open (loading the keys may take a while)

Name it anything you want, but I will name it `soft`.

go to Computer\HKEY_LOCAL_MACHINE\soft\Microsoft\Windows\CurrentVersion\Policies\System

on the right of the Registry editor, scroll to the bottom, right click to create a new key:

the new key has to be in "DWORD (32-bit) Value" and name the new key to "VerboseStatus"

select "VerboseStatus" change its value to 1

at the top of the same key directory, select `EnableCursorSuppression`, change its value to `0`

Close registry editor

### Setup windows boot loader with `bcdboot.exe`

```batch
bcdboot c:\windows /s w:
wpeutil reboot
```

### Setup account and restore start up settings

