# Fedora GNU/Linux tips, tricks and shortcuts

A growing list of tips and tricks useful for efficient usage of Fedora GNU/Linux and Linux in general for software development activities.

## Shortcuts

### Terminal windows operations

Open a tab in a terminal window:
```
Ctrl+Shift+T
```

Switch b/w tabs in a terminal window:
```
Ctrl+PgUp/PgDown
```

### Switching b/w input languaes

```
Ctrl + Space
```
### How to take a screenshot

   Alt + PrtSc: takes a screenshot of a whole screen and saves it into ~/Pictures;
   Shift + PrtSc: takes a screenshot of a selected region of a screen and saves it into ~/Pictures;

## CoreUtils and command line tools

### How to split pipeline output to files on the fly:
```
tail -f rw_algo026.log | split -d -l 1000 - trading_logs/trades-
```
Note: -d allows using numerically indexed output files.

### How to check what ports are opened by a given process by PID

Pass actual PID to the following pipeline:
```
sudo netstat --all --program | grep [PID]
```

### How to connect a video projector

From the command line (Terminal), run:

```
arandr
```

and then set HDMI2 as a primary output.

## Network

## How to open a custom port in the Buffalo AirStation for a web-server

1) find out your external IP:
```
dig TXT +short o-o.myaddr.l.google.com @ns1.google.com
```

E.g. result:
```
"121.103.231.126"
```

2) Go to this IP using your Web-Browser

3) Login to the router (user/pwd are written on the router body)

4) Navigate to Game&Applications page

5) Create a new custom TCP/UDP port, save it (E.g. 8000)

6) To test connectivity, connect from the phone or other device that is not in home network (i.e. is not connected to Wi-Fi), e.g.:
<http://121.103.231.126:8000>

### How to run a simple HTTP file server:

Navigate to the target directory and start Python server, E.g.:
```
python -m SimpleHTTPServer 8000
```

## Fixing things

### How to fix internal microphone

1) start AlsaMixer from the Terminal:

```
alsamixer
```

2) follow steps below:
  * F6 (Select Sound Card);
  * Select Intel PSH;
  * Set Mic Boost to 0%;
  * Bump up Internal Mic level to 25-50%.

Note: alternative tuner is: pavucontrol.

### How to adjust the screen brightness if it got stuck

Edit "grub" file:
```
vim /etc/default/grub
```

appending "acpi_backlight=vendor" at the end of the GRUB_CMDLINE_LINUX setting, E.g., changing:

```
GRUB_CMDLINE_LINUX="rd.lvm.lv=fedora/swap rd.lvm.lv=fedora/root rhgb quiet"
```

to:

```
GRUB_CMDLINE_LINUX="rd.lvm.lv=fedora/swap rd.lvm.lv=fedora/root rhgb quiet acpi_backlight=vendor"
```

### How to move space from a dual boot Windows partition to Fedora and use it to extend Fedora home partition

First, delete existing dual-boot partition:
```
sudo fdisk /dev/sda

# type 'p', which stands for print and browse available partitions

# type 'd', which stands for delete
d

# type the number of partition to be deleted, E.g. 5
5

# write to the disk
w
```

Create a new partition that will be used by Fedora:
```
sudo fdisk /dev/sda

# type 'p', which stands for print and browse available partitions

# type 'n', which stands for new partition creation
n

# select new partition number (E.g. type: 7):
7

# keep pressing enter to create a new partition with size equal to the amount of free space on the disk

# write to the disk
w
```

Reboot the system.

Create a new device on top of the newly created partition:
```
sudo pvcreate /dev/sda7
```

Extend Fedora to use the new device:
```
sudo vgextend fedora /dev/sda7
```

Extend Fedora home to use the new partition:
```
sudo lvextend -L150G /dev/mapper/fedora-home
```

Resize Fedora home to give all the newly added space:
```
sudo resize2fs /dev/mapper/fedora-home
```

Confirm that Fedora home has been extended:
```
df -h
```

Sample output:
```
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/fedora-home  148G   26G  116G  18% /home
```
