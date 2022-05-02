# Hardware

- Del R720 2x INTEL XEON DECA CORE E5-2650Lv2 1.7GHZ 10CORE 20THREADS / 64 GB Ram  (Refurbished)
- 1 TB USB HDD
- 64 GB USB Pen
- 64 GB USB Pen

# Install ESXi

- Burn ESXI ISO  with RUFUS on 64 GB Pen
- 32 GB Pendrive plug in The DEL R720 motherboard
- 64 GB Pendrive plug in Front panel
- F11 UEFI Bios select front panel Pendrive, boot.
- After the Installation complted setub ESXi

# Setup ESXi Outside

- Disable IPV6
- Setup static IPV4
- Router setup Fix ip for DEL R720


# Setup ESXi in webclient

- Network
- TCP/IP stacks 
- Default Tcp/ip stack
- Edit settings
- set hostname: " esxi-01 "
- set hostname: " lab.local "
- set Search domains: " lab.local "

- Manage / System / Time and Date
- Edit Ntp settings 
- Use Network Time Protocol (enable NTP client) 
- Start and Stop manually
- 0.pool.ntp.org, 1.pool.ntp.org, 2.pool.ntp.org, 3.pool.ntp.org
- Manage / Services
- Start SSH services
- Start NTP Daemon 

# Setup usb storage

- open power shell

      ssh-keygen -R [hostname-or-IP]
      ssh root@111.111.111.11
      /etc/init.d/usbarbitrator stop
      chkconfig usbarbitrator off
# in ESXi cheking Storage / Devices / 64 GB Pen

      partedUtil mklabel /dev/disks/mpx.vmhba35:xxxxx gpt
      
      eval expr $(partedUtil getptbl /dev/disks/mpx.vmhba35:xxxxx | tail -1 | awk '{print $1 " \\* " $2 " \\* " $3}') - 1

      partedUtil setptbl /dev/disks/mpx.vmhba35:xxxxx gpt "1 2048 121065839 AA31E02A400F11DB9590000C2911D1B8 0"

      vmkfstools -C vmfs6 -S USB_64GB_Pen_Datastore /dev/disks/mpx.vmhba35:Cxxxx
 
 # in ESXi cheking Storage / Devices / 1 TB USB HDD

       partedUtil mklabel /dev/disks/mpx.vmhba36:xxxxx gpt

       eval expr $(partedUtil getptbl /dev/disks/mpx.vmhba36:xxxx | tail -1 | awk '{print $1 " \\* " $2 " \\* " $3}') - 1

       partedUtil setptbl /dev/disks/mpx.vmhba36:xxxxx gpt "1 2048 1953455804 AA31E02A400F11DB9590000C2911D1B8 0"

       vmkfstools -C vmfs6 -S USB-HDD-1TB-Datastore /dev/disks/mpx.vmhba36:1

# Install VMCenter and Sphere

- Donwload VMcenter ISO
- Mount, and browse VCSA UI Installer / Win32 / 
- Installer.exe
- Add Esx01 IP and pass
- Setup and deploy
- when the install is complete, setup vCenter

# Setup Vsphere in webclient

- Add new Data center
- Add host: ESXi01

# Error message because use usb storage

- " System logs on host 1111.111.11.11 are stored on non-persistent storage. "
- In the vSphere Client navigator, select the Hosts and Clusters view
- Select the host  object in the vSphere Client navigator.
- Click the Configure tab, then the System expander
- Under System, click Advanced System Settings.
- Ensure that Syslog.global.logDir points to a persistent location.
- CurrentScratchLocation shows a location on persistent storage.
- rewrite log " [USB-HDD-1TB-Datastore] Esx_01_log "
- If the folder being used as a scratch location is to be shared by multiple ESXi hosts, you should also set the field Syslog.global.logDirUnique to avoid contention over log files.

# Error message not supported CPU type

- " No coredump target has been configured "
- Go to the host with the warning, then navigate to Configure > 
- System > Advanced System Settings.
- Click Edit on the top right.
- Filter by UserVars.Suppress and find UserVars.
- SuppressCoredumpWarning.
- Change the value from 0 to 1, then click OK