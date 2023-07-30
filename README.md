# XDG

Add file `/etc/profile.d/xdg-cache.sh` with

```
export XDG_CACHE_HOME="/tmp/.cache"
```

# VLC

In ubuntu the video output is defined in the file `$HOME/snap/vlc/common/vlcrc`

```
# Video output module (string)
vout=xcb_xv
```
or use `/snap/bin/vlc --vout=xcb_xv`


# adb enable rescan media

```
adb shell am broadcast -a android.intent.action.MEDIA_SCANNER_SCAN_FILE -d file:///storage/emulated/0/DCIM/Camera/
adb shell content query --uri "content://media/external/file"
```


# Thinkpad

## Touchpad
```
#!/bin/bash
declare -i ID
ID=`xinput list | grep -Eo 'TouchPad\s*id\=[0-9]{1,2}' | grep -Eo '[0-9]{1,2}'`
xinput set-prop $ID "Device Enabled" 1
echo 'Touchpad has been enabled.'
```

## Enable thinkpad keyboard backlight after resume
Create file with `rx` permisions `/usr/lib/pm-utils/sleep.d/01keyboardbacklight` and add

```
#!/bin/bash

#apt-get install -y brightnessctl
brightnessctl --device='tpacpi::kbd_backlight' set 1 >/dev/null 2>/dev/null
```


# ffmpeg

```
ffmpeg -i input.mp3 -ss 60 output.mp3 # skip 60 seconds at start
ffmpeg -i input.mp3 -to 00:52:00 output.mp3 # Stop writing the output at time
ffmpeg -i input.mp4 -vf scale=1920:-1 -vcodec libx265 -crf 24 output.mp4 # mastodon
```

# Last installed packages

```
grep --color=auto "install" /var/log/dpkg.log | awk '$3~/^install$/ {printf $4" ";}'
```


# Minimal Debian or ubuntu

- Minimal Debian or ubuntu https://wiki.debian.org/ReduceDebian
- To restore ``` apt-get install groff groff-base ```

## Rename

- To rename all files so that they are compatible with FAT32

```
find ./DIRECTORY -maxdepth 1  -exec rename -n 's/[^[:ascii:]]/_/g' {} \;
```


## ChromeOS


### recovery 

- https://chromiumdash.appspot.com/serving-builds?deviceCategory=ChromeOS

- https://cros.tech/

### subtitles player

- https://github.com/guancio/ChromeOsSubtitle

### Enable developer mode

Mount the flex SSD in a linux device (or USB boot). 

sudo ```fdisk -l``` to identify the EFI System partition for example ```/dev/sdb12```. Then mount that partition.

Edit file ```grub.cfg``` and add ```cros_debug``` to the various options

Saved the ```grub.cfg``` file

For meltdown/spectre add ```kvm-intel.vmentry_l1d_flush=always``` to the kernel command line the same way you added ```cros_debug``` to enable some software mitigations.


## Change keyboard layout Chromebook

- Put in developer mode by Hold down the Escape and Refresh keys.
- Click the power button. You can release the Escape and Refresh keys at this point.
- Once you see the Recovery screen, hit Ctrl-D.
- Wait a while. The Chromebook will beep at you once or twice.
- Eventually, it’ll boot back up to the normal Chrome OS login screen.
- Hit Ctrl-Alt-F2 (F2 is the right-facing arrow next to the refresh key) to get to a login prompt.

```
login as chronos (no password needed)
sudo -s
vpd -l  # to list current status
vpd -s region=de
```

- it was NOT necessary to remove battery (i.e) modern write protect


### Chromebook writable root


First make sure your machine is set to developer mode and then reboot to apply any pending updates.

Execute the following to remove the root FS verification:

```sudo /usr/share/vboot/bin/make_dev_ssd.sh --remove_rootfs_verification```

The command will fail with a warning and tell you to run the same command with ```--partitions 4``` flag at the end where 4 can be any number. Run the command again with the new flag.

Then set the following variable:

```sudo crossystem dev_boot_signed_only=0```

Then remount your root FS with:

```sudo mount -o remount,rw /```




References:

https://www.reddit.com/r/ChromeOSFlex/comments/swxlz8/tutorial_enable_developer_mode_on_cros

https://www.reddit.com/r/chromeos/comments/stl9fq/enabled_developer_mode_on_chromeos_flex_by/

https://old.reddit.com/r/ChromeOSFlex/comments/w3ehw7/fixing_audio_on_bay_trail_chromebooks/

https://xn--1ca.se/chromebook-writable-root/




# systemd suspend modules

```
#!/bin/bash

# Put into /lib/systemd/system-sleep/suspend-modules
# chmod a+x /lib/systemd/system-sleep/suspend-modules

# Unloads kernel modules defined in /etc/suspend-modules.d/*.conf
# and /etc/suspend-modules
# with one module per line

# Too see credits, see git history
# https://gist.github.com/sigboe/2602f9318b8f55ca92c7755a5b70644d/edit

case $1 in
    pre)
        for mod in $(cat /etc/suspend-modules 2> /dev/null; awk 1 /etc/suspend-modules.d/*.conf 2> /dev/null); do
            rmmod $mod
        done
    ;;
    post)
        for mod in $(cat /etc/suspend-modules 2> /dev/null; awk 1 /etc/suspend-modules.d/*.conf 2> /dev/null); do
            modprobe $mod
        done
    ;;
esac
```


# ddcutil

- The following command changes the Brightness and Contrast inside the external monitor, respectively.
```
root@linux:~# ddcutil getvcp 10
VCP code 0x10 (Brightness                    ): current value =    40, max value =   100
root@linux:~# ddcutil capabilities | grep "Brightness\|Contrast"
   Feature: 10 (Brightness)
   Feature: 12 (Contrast)
```
- `ddcutil setvcp 10 40`
- `ddcutil setvcp 12 40`

# Chromium packages

- Linux: https://launchpad.net/~savoury1/+archive/ubuntu/chromium
- `chromium-browser --headless --disable-gpu "https://example.com" --dump-dom 2>&1 | pup`
- `chromium-browser --headless --disable-gpu "https://example.com" --print-to-pdf 2>&1 `

# Android

- OTAs are downloaded is android.googleapis.com  ota.googlezip.net

# gphotos sync

- `pip3 install --force-reinstall  gphotos-sync`
- `~/.local/bin/gphotos-sync  --index-only --use-flat-path --progress --rescan --flush-index /tmp/Pictures`
- Edit `.local/lib/python3.9/site-packages/gphotos/GooglePhotosIndex.py`

```diff
-       "Indexed %d %s", self.files_indexed, media_item.relative_path
+       "https://photos.google.com/lr/photo/%s %s", media_item.id, media_item.filename
```


# PDF

- Split and rearrange: https://freesplit.app/

# Annotations

- Quick: https://szoter.com/launch/ 
- Standalone: https://github.com/ksnip/ksnip


# fstrim

```
cat /etc/crypttab 
# <target name>	<source device>		<key file>	<options>
home  UUID=783989fd-d44c-4e49-9287-a6ede15d7e13  none  luks,discard,no-read-workqueue,no-write-workqueue
```

- Enable

```
# cryptsetup --perf-no_read_workqueue --perf-no_write_workqueue --allow-discards --persistent  refresh home
```

- Verify

```
# cryptsetup luksDump  /dev/nvme0n1p4 | grep Flags
Flags:       	allow-discards no-read-workqueue no-write-workqueue
```


```
# dmsetup table 
home: 0 905183232 crypt aes-xts-plain64 :64:logon:cryptsetup:56a88c26-0843-4be9-b333-d79a1209c166 0 259:4 32768 3 allow_discards no_read_workqueue no_write_workqueue
```


# Xresources

- lower numbers make fonts small (hard to read)
- For a 14 inch screen 135 is good
- Edit `.Xresources`

```
Xft.dpi: 135
```

| Size | dpi |
|--|--|
| 14 | 135 |
| 17 | 128 |

# List only security updates

```
apt-get --just-print upgrade | grep -i security   | awk '{print $2}'
```


# YouTube autoplay disable

https://raw.githubusercontent.com/DandelionSprout/adfilt/master/StopAutoplayOnYouTube.txt


# solokey

```sudo apt install solo-python
solo key version
4.0.0 locked
```

- `solo key wink`
- `solo key update`
- `solo key update`
```
Wrote temporary copy of firmware-4.1.5.json to /tmp/tmp7tfs8v2f.json
sha256sums coincide: f36bb365bfddf75004f28af392ae1439192ca0ed821ef496084a75a00d05087a
using signature version >2.5.3
erasing firmware...
updated firmware 100%             
time: 9.24 s
bootloader is verifying signature...
...pass!

Congratulations, your key was updated to the latest firmware version: 4.1.5
```


# fsarchiver rsync full system backup

```
#fsarchiver savefs /home/user/FSARCHIVER/10.01.2022-nvme0n1p2-root.fsa /dev/nvme0n1p2 -s 900 --exclude=/snap --exclude="/home/*"  --exclude='/media' --exclude='/var/lib/lxd/*' --exclude='/tmp/*' --exclude='/var/lib/snapd/*' -A -v
#rsync -aAXHv -x  --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found","/snap/*","/home/*"}  /   /media/500GB/SYSTEM_ROOT/
```



# Email encoder

- https://freetools.dev/email-encoder
- https://hoax-info.tubit.tu-berlin.de/software/emailencoder.shtml


# Google cloud

- https://console.cloud.google.com/marketplace/product/google/drive.googleapis.com
- https://groups.google.com/a/chromium.org/g/google-browser-signin-testaccounts
- https://groups.google.com/a/chromium.org/g/chromium-dev
- https://console.cloud.google.com/apis/api/chromesync.googleapis.com

## optional

- https://console.cloud.google.com/marketplace/product/google/cloudsearch.googleapis.com
- https://console.cloud.google.com/marketplace/product/google/safebrowsing.googleapis.com
- https://console.cloud.google.com/marketplace/product/google/timezone-backend.googleapis.com
- https://console.cloud.google.com/marketplace/product/google/admin.googleapis.com
- 💱 https://console.cloud.google.com/marketplace/product/google/translate.googleapis.com
- https://console.cloud.google.com/marketplace/product/google/embeddedassistant.googleapis.com
- https://console.cloud.google.com/marketplace/product/google/calendar-json.googleapis.com
- https://console.cloud.google.com/marketplace/product/google/copresence.googleapis.com

# Cloudready

`sudo dd if=cloudready.bin of=/dev/sdX bs=4M `

- If you add this to Ventoy rename the image to `.img`

- Commandline installation

`Ctrl+Alt+T` or `Ctrl+Alt+F2`

- You'll need to log in - use the username: `chronos` and the password: `chrome`

- `sudo fdisk -l | grep mmc\|sda`

- Install

```
cd /usr/sbin
sudo chromeos-install --dst /dev/sdX 
or
sudo chromeos-install --skip_src_removable --dst /dev/sdX
```



# Disable auto update of secureboot keys

- check for secureboot

`mokutil --sb-state`

```
systemctl stop secureboot-db.service
systemctl disable secureboot-db.service
journalctl -o short-precise -b -u secureboot-db.service
```

# test for hibernate
`user@laptop: ~ $ busctl call org.freedesktop.login1 /org/freedesktop/login1 org.freedesktop.login1.Manager CanHibernate`

the result

`s yes `

# yt-dlp
```
yt-dlp -f 'w[ext=mp4]'
yt-dlp --min-sleep-interval 2 --max-sleep-interval 4  -f 'b' -S 'filesize~100M'
yt-dlp --output "%(title)s.%(ext)s" --restrict-filenames -x --audio-format mp3 --match-filters "title~='(?i)\bSTRING-HERE\b'" 
```


# subsync
```
/snap/bin/subsync -c sync -s old_subtitle.srt -r input.mp4 --ref-lang eng --sub-lang=eng --ref-stream-by-type=audio --out corrected_subtitle.srt
snap connect subsync:removable-media

```

# Gcloud
`curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`

# Fdroid repos
https://github.com/bmrz2019/foxy-droid/releases/download/v1.5/foxy-droid-debug.apk

# Sony SRS XB20
Reset: With the speaker turned on, press and hold the – (volume) button and the Power button at the same time for more than 5 seconds until the speaker turns off.

# inxi
`inxi -Fxxrzc0`

# RSS podcasts

```
wget   https://example.com/something.rss
xmlstarlet sel -t -v "//media:content/@url"  something.rss| grep mp3  |  sed -e 's/?.*//' > list
wget --random-wait --wait 120 --no-clobber -i list

```

# encfs

```
sudo apt install encfs
encfs ~/.encrypted ~/visible
fusermount -u ~/visible

```

# fat32

```
mkfs.vfat -s 16 -S 4096
```

# Samba

```
apt-get install samba tdb-tools samba-common-bin samba-common python3-samba python3-tdb python3-dnspython
systemctl disable smbd.service nmbd.service
chown nobody:nogroup /path/to/share # or give rx permissions to the directory of share
```

edit ` /etc/samba/smb.conf`

```
[share]
    comment = Ubuntu File Server Share
    path = /path/to/share
    browsable = yes
    guest ok = yes
    read only = yes
    create mask = 0755
```


### For use of samba inside LXD (Xenial)

```
root@host:~# service smbd restart
root@host:~# netstat -apn | grep 445
tcp        0      0 127.0.0.1:445           0.0.0.0:*               LISTEN      13390/smbd      
tcp        0      0 a.b.c.d:445        0.0.0.0:*               LISTEN      13390/smbd      
tcp        0      0 10.147.20.1:445         0.0.0.0:*               LISTEN      13390/smbd      
tcp        0      0 a.b.c.d:445        a.b.c.55:3029       TIME_WAIT   -               
tcp        0      0 a.b.c.d:445        a.b.c.44:35502      FIN_WAIT2   -               
tcp        0      0 a.b.c.d:445        a.b.c.55:3031       ESTABLISHED 13395/smbd      
tcp6       0      0 ::1:445                 :::*                    LISTEN      13390/smbd      
unix  2      [ ACC ]     STREAM     LISTENING     22445    1/init              /run/snapd.socket
root@host:~# netstat -apn | grep 445
tcp        0      0 127.0.0.1:445           0.0.0.0:*               LISTEN      13390/smbd      
tcp        0      0 a.b.c.d:445        0.0.0.0:*               LISTEN      13390/smbd      
tcp        0      0 10.147.20.1:445         0.0.0.0:*               LISTEN      13390/smbd      
tcp        0      0 a.b.c.d:445        a.b.c.55:3029       TIME_WAIT   -               
tcp        0      0 a.b.c.d:445        a.b.c.44:35502      FIN_WAIT2   -               
tcp        0      0 a.b.c.d:445        a.b.c.55:3031       ESTABLISHED 13395/smbd      
tcp6       0      0 ::1:445                 :::*                    LISTEN      13390/smbd      
unix  2      [ ACC ]     STREAM     LISTENING     22445    1/init              /run/snapd.socket
root@host:~# ps axf | grep smbd
13405 pts/3    S+     0:00  |                   \_ grep --color=auto smbd
13236 ?        Ss     0:00      \_ /usr/sbin/smbd -D
13239 ?        S      0:00          \_ /usr/sbin/smbd -D
13244 ?        S      0:00          \_ /usr/sbin/smbd -D
13390 ?        Ss     0:00 /usr/sbin/smbd -D
13391 ?        S      0:00  \_ /usr/sbin/smbd -D
13393 ?        S      0:00  \_ /usr/sbin/smbd -D
13395 ?        S      0:00  \_ /usr/sbin/smbd -D
```


If you run samba server in both lxd-containers and the lxd-host then you may run to smb starting problems in host.
Edit file `/etc/init.d/smbd`
```
diff --git a/smbd b/smbd
index b6ec38f..1e92d8e 100755
--- a/smbd
+++ b/smbd
@@ -37,7 +37,7 @@ case $1 in
      # Make sure we have our PIDDIR, even if it's on a tmpfs
      install -o root -g root -m 755 -d $PIDDIR

-     if ! start-stop-daemon --start --quiet --oknodo --exec /usr/sbin/smbd -- -D; then
+      if ! start-stop-daemon --start --quiet --oknodo --pidfile /var/run/samba/smbd.pid --exec /usr/sbin/smbd -- -D; then

         log_end_msg 1
         exit 1

```

Verify with

```
root@host:~# ps -ef | grep smbd
100000   13236 12434  0 09:29 ?        00:00:00 /usr/sbin/smbd -D
100000   13239 13236  0 09:29 ?        00:00:00 /usr/sbin/smbd -D
100000   13244 13236  0 09:29 ?        00:00:00 /usr/sbin/smbd -D
root     13390     1  0 09:30 ?        00:00:00 /usr/sbin/smbd -D
root     13391 13390  0 09:30 ?        00:00:00 /usr/sbin/smbd -D
root     13393 13390  0 09:30 ?        00:00:00 /usr/sbin/smbd -D
root     13395 13390  0 09:30 ?        00:00:00 /usr/sbin/smbd -D
root     13435 11720  0 09:35 pts/3    00:00:00 grep --color=auto smbd
```

# Firefox
 from https://news.ycombinator.com/item?id=16497642
	
For privacy, on a linux box are there any downsides to simply creating one or more extra accounts, and running Firefox in them for privacy ('DISPLAY=:0 firefox')?. I use this approach to set up firefox as I like it on a spare account, then copy '.mozilla' to '.mozilla-base'. Then it's just a simple case of 'su -l guest' and (via a script) 'rm -fr ~/.mozilla; cp -a ~/.mozilla_base .mozilla; DISPLAY=:0 firefox; rm -fr ~/.mozilla' (actually the script deletes the local cache as well).
Net effect is that firefox starts exactly as I like, but forgets everything that happened in the session ('groundhog-day mode').

Edit: added 'su -l' step.

Edit: As an adendum, note that this technique can be extended to the complete 'guest' accounts as well, e.g. 'cd /home; rm -fr guest; cp -a guest.base guest; su -l guest'; the entire 'guest' account is then 'groundhog-dayed'.

```
  #!/bin/sh
  #
  export DISPLAY=:0
  # Set up clean copy
  cd ~
  rm -fr .mozilla
  cp -a .mozilla_base .mozilla
  cd - > /dev/null
  #
  /usr/local/bin/firefox $@
  #
  echo "Holding...."
  sleep 2
  echo "Cleaning...."
  # Clean out junk (so we start clean next time)
  cd ~
  rm -fr .mozilla .cache/mozilla*
  rm -fr .adobe
  rm -fr .macromedia
  cd - > /dev/null
```

alternate option

```
    firejail --jail /tmp/firefox /usr/local/bin/firefox

```


# Block ads

http://someonewhocares.org/hosts/

http://pgl.yoyo.org/adservers/serverlist.php?hostformat=dnsmasq-server&showintro=1&mimetype=plaintext

https://github.com/StevenBlack/hosts

# Grub reinstallation mainly on ubuntu.
Boot with sysrescuecd. If the root is at `sda2` then do the following
```
mkdir /mnt/2
mount /dev/sda2 /mnt/2
mount -o bind /dev /mnt/2/dev
mount -o bind /sys /mnt/2/sys
mount -t proc /proc /mnt/2/proc
```
Now you should be able to
```
chroot /mnt/2 bash
```
If there was no bash installed on the 'failed' OS, the use
```
chroot /mnt/2 /sbin/sh
```
Now time to reinstall grub.
```
grub-install /dev/sda
```
You can try a `update-grub`



# Clone your favorite OS
```
sfdisk -d /dev/sda
sfdisk -d /dev/sda > /tmp/backup-partition-file-sda.bak
```
To restore to a new drive
```
sfdisk /dev/sdb < /tmp/backup-partition-file-sda.bak
```
To directly copy to a new disk
```
sfdisk -d /dev/sda | sfdisk /dev/sdb
```
If you need to backup bootsector/MBR. I do not know why and which command works but running these sucessively made it work. This works only if both disks have same size and partitions. (May be they can be smaller?)

```
dd if=/dev/sda of=/dev/sdb bs=512 count=1
dd if=/dev/sda of=/dev/sdb bs=512 count=63
```


# LXD

```
lxc config device set megasyn-vm  root  size 10GB
lxc config set myn-vm limits.memory 512MB
lxc config set rea-vm3 limits.cpu 1
lxc config set rea-vm3 boot.autostart 1
lxc config set remotus boot.autostart.delay  30
lxc config set core.trust_password a_long_string_may_be
lxc config set core.https_address "[::]:8443"
sudo lxd-p2c https://destination.ip:8443 new-container-name /
```

Changing your storage pool

```
lxc storage list
lxc profile device add default root disk path=/ pool=default
```

Minimal image

``` 
lxc remote add --protocol simplestreams ubuntu-minimal https://cloud-images.ubuntu.com/minimal/releases/
lxc launch ubuntu-minimal:xenial
lxc launch ubuntu-minimal:xenial -t t2.nano

``` 
For a list see https://github.com/dustinkirkland/instance-type/blob/master/tab/aws



How to setup LXD

```
sudo apt-get install bridge-utils
sudo lxd init
Name of the storage backend to use (dir or zfs): dir
Would you like LXD to be available over the network (yes/no)? yes
Do you want to configure the LXD bridge (yes/no)? yes
Would you like to setup a network bridge for LXD containers now? no  
Do you want to use an existing bridge? yes  
Bridge interface name:  br0
```
Setup enable packet forwarding in file `/etc/sysctl.conf` for IPv4 on the LXD host
```
net.ipv4.ip_forward=1
```
Run `sudo sysctl -p` or `reboot`. Now change the adapt ths file describes the network `/etc/network/interfaces` in host for static to bridge `br0` changes

```
root@mg50:~# cat /etc/network/interfaces
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

auto br0
iface br0 inet static
   address bare.metal.host.ip
   netmask 255.255.255.224
   network a.b.c.d
   gateway a.b.c.d
   dns-nameservers a.b.c.d
   dns-search host.domain
   bridge_ports eno1


auto br1
iface br1 inet manual
    bridge_ports enxlkldkflkdkfj

```


Verify before restarting network as you lose connection 

```
ip addr flush eno1 &&  ip addr flush enxlkldkflkdkfj  && systemctl restart networking.service
```



If everything was OK, you will see `br0´, `eth0 or enp2s0` and `lo` when you run `ifconfig` like this (redacted partially)

```
br0       Link encap:Ethernet  HWaddr 00:
          collisions:0 txqueuelen:1000 
          RX bytes:417554630 (417.5 MB)  TX bytes:8047958 (8.0 MB)

br1    Link encap:Ethernet  HWaddr 00:
          RX bytes:744425912 (744.4 MB)  TX bytes:14523858 (14.5 MB)


eno1      Link encap:Ethernet  HWaddr d4::::::


enx0022cf932bbc Link encap:Ethernet  HWaddr 00:2::::::  


lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          RX bytes:11840 (11.8 KB)  TX bytes:11840 (11.8 KB)
```

IIRC, `lxc profile show default ` shows
```lxc profile show default
config:
  environment.http_proxy: ""
  user.network_mode: ""
description: Default LXD profile
devices:
  eth0:
    name: eth0
    nictype: bridged
    parent: br1
    type: nic
  root:
    path: /
    pool: default
    type: disk
name: default
used_by:
- /1.0/containers/climbing-crow

```
Time to create, launch containers. The first time it takes long to download, may be unzip and launch containers.
```sudo lxc launch ubuntu:xenial host222xenial
Creating host222xenial
Starting host222xenial
```
Let see what is running
```
lxc list
+---------------+---------+--------------------------------+------+------------+-----------+
|     NAME      |  STATE  |              IPV4              | IPV6 |    TYPE    | SNAPSHOTS |
+---------------+---------+--------------------------------+------+------------+-----------+
| host222xenial | RUNNING |                                |      | PERSISTENT | 0         |
+---------------+---------+--------------------------------+------+------------+-----------+
```
As you see above it did not get a IPV4 address. To do that `sudo lxc exec host222xenial bash` will log you inside `host222xenial`. Do not edit `/etc/network/interfaces` but edit `/etc/network/interfaces.d/50-cloud-init.cfg`
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

```
Change it to give static ip, gateway, DNS. For some reason, the containers always have eth0 and not the persistent enp* naming scheme. Verify with a `dmesg` if needed.

```
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface of the container
auto eth0
iface eth0 inet static
   address 1.2.3.222
   netmask 255.255.255.240
   network 1.2.3.112
   broadcast 1.2.3.127
   gateway 1.2.3.126
   # dns-* options are implemented by the resolvconf package, if installed
   dns-nameservers 8.8.8.8 4.4.4.4
``` 
Either restart container or may be you can reload networking `sudo lxc restart  host222xenial`

Let see what is running
```
lxc list
+---------------+---------+--------------------------------+------+------------+-----------+
|     NAME      |  STATE  |              IPV4              | IPV6 |    TYPE    | SNAPSHOTS |
+---------------+---------+--------------------------------+------+------------+-----------+
| host222xenial | RUNNING |       1.2.3.222                |      | PERSISTENT | 0         |
+---------------+---------+--------------------------------+------+------------+-----------+
```

To rename host use `lxc move host222xenial host444xenial`

To launch centos `sudo lxc launch images:centos/7/amd64 centos333`

To launch trusty `sudo lxc launch ubuntu:trusty host222`

If you need `screen` inside a container then use

`lxc exec host222xenial -- sh -c "exec >/dev/tty 2>/dev/tty </dev/tty && /usr/bin/screen -s /bin/bash"`




# Tips
for enabling softkeys in android phone just add
```
qemu.hw.mainkeys=0
to /system/build.prop
```

# ZFS
## install ubuntu
```
sudo apt install zfs zfs-dkms zfs-zed zfs-initramfs zfsutils-linux zfsutils-linux
```

## commands

```
sudo zpool create data2 /dev/sdc
#ls --color=never -lh /dev/disk/by-id/
sudo zpool create data2 ata-ST4000DM003-1ER162_AAAAAAAL
sudo zpool status zpool
sudo zfs get all zpool
zfs create <nameofzpool>/<nameofdataset>
sudo zfs set compression=lz4 mypool
sudo zfs get compressratio
sudo zfs list
sudo zfs get used,compressratio,compression,logicalused
chown -R user.user /nameofzpool/datasets

```

## commands
```
find  .  -type f \( -name '*.img' -o -name '*.mccd' -o -name '*.cbf' \) -print0 | xargs -0 gzip -v --best

function jgrep()
{
    find . -name .repo -prune -o -name .git -prune -o  -type f -name "*\.java" -print0 | xargs -0 grep --color -n "$@"
}

function cgrep()
{
    find . -name .repo -prune -o -name .git -prune -o -type f \( -name '*.c' -o -name '*.cc' -o -name '*.cpp' -o -name '*.h' \) -print0 | xargs -0 grep --color -n "$@"
}

resgrep () 
{ 
    for dir in `find . -type d \( -not -name "values-*" -a -not -name "mipmap*" -a -not -name "drawable-*" -a -not -name "." \)`;
    do
        find $dir -type f -name '*\.xml' -exec grep --color -n "$@" {} +;
    done
}

function mangrep()
{
    find . -name .repo -prune -o -name .git -prune -o -path ./out -prune -o -type f -name 'AndroidManifest.xml' -print0 | xargs -0 grep --color -n "$@"
}

function sgrep()
{
    find . -name .repo -prune -o -name .git -prune -o  -type f -iregex '.*\.\(c\|h\|cpp\|S\|java\|xml\|sh\|mk\)' -print0 | xargs -0 grep --color -n "$@"
}

function mgrep()
{
    find . -name .repo -prune -o -name .git -prune -o -path ./out -prune -o -regextype posix-egrep -iregex '(.*\/Makefile|.*\/Makefile\..*|.*\.make|.*\.mak|.*\.mk)' -type f -print0 | xargs -0 grep --color -n "$@"
}

function treegrep()
{
    find . -name .repo -prune -o -name .git -prune -o -regextype posix-egrep -iregex '.*\.(c|h|cpp|S|java|xml)' -type f -print0 | xargs -0 grep --color -n -i "$@"
}

function findfile()
{
    find . -name .repo -prune -o -name .git -prune -o -path ./out -prune -o -type f -iname "$@"| grep -v ".git" | grep -v ".repo"
}

function finddir()
{
    find . -name .repo -prune -o -name .git -prune -o -path ./out -prune -o -type d -iname "$@"| grep -v ".git" | grep -v ".repo"
}

```


# CPU

```
./kcbench -b -j 4 -s ./ -i 1 | tee acer-silver.log
Processor:           Intel(R) Pentium(R) Silver N6000 @ 1.10GHz [4 CPUs]
Cpufreq; Memory:     powersave [intel_pstate]; 3728 MiB
Linux running:       5.15.0-47-generic [x86_64]
Compiler:            gcc (Ubuntu 11.2.0-19ubuntu1) 11.2.0
Linux compiled:      6.0.9 [/linux-6.0.9/./]
Config; Environment: defconfig; CCACHE_DISABLE="1"
Build command:       make vmlinux
Run 1 (-j 4):        1010.36 seconds / 3.56 kernels/hour [P:391%, 1678 maj. pagefaults]

./kcbench -b -j 4 -s ./ -i 1
Processor:           Intel(R) Core(TM) i3-1005G1 CPU @ 1.20GHz [4 CPUs]
Cpufreq; Memory:     powersave [intel_pstate]; 7605 MiB
Linux running:       5.15.0-47-generic [x86_64]
Compiler:            gcc (Ubuntu 11.2.0-19ubuntu1) 11.2.0
Linux compiled:      6.0.9 [/linux-6.0.9/./]
Config; Environment: defconfig; CCACHE_DISABLE="1"
Build command:       make vmlinux
Run 1 (-j 4):        508.25 seconds / 7.08 kernels/hour [P:391%, 1464 maj. pagefaults]

./kcbench -b -j 16 -s ./ -i 1 | tee thinkpad.log
Processor:           AMD Ryzen 7 PRO 5850U with Radeon Graphics [16 CPUs]
Cpufreq; Memory:     schedutil [amd-pstate]; 11788 MiB
Linux running:       5.15.0-47-generic [x86_64]
Compiler:            gcc (Ubuntu 11.2.0-19ubuntu1) 11.2.0
Linux compiled:      6.0.9 [/linux-6.0.9/./]
Config; Environment: defconfig; CCACHE_DISABLE="1"
Build command:       make vmlinux
Run 1 (-j 16):       178.47 seconds / 20.17 kernels/hour [P:1497%, 2450 maj. pagefaults]

```

# Android Go

```
build/make/target/product/go_defaults_512.mk
build/make/target/product/go_defaults_common.mk
build/make/target/product/go_defaults.mk
build/make/target/board/go_defaults_512.prop
build/make/target/board/go_defaults_common.prop
build/make/target/board/go_defaults.prop
```



# Pixel

```
storage.googleapis.com
afwprovisioning-pa.googleapis.com
www.gstatic.com
googlehosted.l.googleusercontent.com
ota-cache1.googlezip.net
dl.google.com
instantmessaging-pa.googleapis.com
www.google.com
ssl.gstatic.com
ota.googlezip.net
digitalassetlinks.googleapis.com
clients.l.google.com
gstatic.com
mobile-gtalk.l.google.com
mobile.l.google.com
lpa.ds.gsma.com
connectivitycheck.gstatic.com
app-measurement.com
```


# Pixel battery

```
I cut the Battery Management PCB off of the battery and soldered the output of the buck converter to the B+ and B- pads (remember to check polarity). I then soldered another wire to the B- pad and grounded it on the screw that secures the LCD.  4.2V 5A Buck Converter.

You will need to cut the small battery management PCB from the battery. Solder 3 wire to the battery management board. (1 wire to battery [B+], and 2 wire to battery[B-]). Connect one of 2 wire that connected to battery [B-] terminal to a ground point of the pixel main PCB. Supply the power for the phone with about 4.2V PSU. I tried some ground points of the pixel main board. Some point will allow the phone power up but cannot pass the boot screen. The most easy and fully working ground point is the bolt that hold LCD cable(Check the image below ).

I managed to also make mine work, but ended up using less hardware. I have an old Oneplus brick that supplies 5V 4A. I took that and wired it up to the board exactly like everyone else. So far it's running. I want to run it for awhile before considering it a complete great success.

```

