# Debian-Mobian Bookworm F2FS Nexus 7 2012 wifi rev. E1565 kernel 5.14-rc3-next-grate

Link trên Google drive:

Default user was debian, default passwd was 123456

Debian Mobian Bookworm f2fs rootfs

https://drive.google.com/drive/folders/1PgR3LVJa7UyHzY9PA_xc6XeCYpSkhLEI?usp=sharing



Debian Bulleye pre-image:

https://drive.google.com/drive/mobile/folders/1jhq1v5ejOazDB1wSF-ZS0FxBc_QIPUFD/1BL_ZPgsDuYTu5EXl0vXK6uyIeGmW5FbW?sort=13&direction=a



Vì mục đích testing Phosh Mobile Gnome-shell trên Nexus 7 2012 mà mình đã làm ra ubuntu 21.04 hirsute hippo, nhưng Phosh chưa chạy được nên build Debian để cài và thành công. Vì Phosh Mobile gnome-shell đã chạy trên N7 postmarketOS nhưng do quá ít ứng dụng cài nên mới thử trên distro khác xem có tiện lợi hơn không



***Cần có micro-usb keyboard để input trong command line



Cách kiểm tra Nexus 7 2012 là mã cũ PM269 hay E1565. Tham khao:

https://wiki.postmarketos.org/wiki/Google_Nexus_7_2012_(asus-grouper)



Variants

grouper rev. PM269 - without GSM (oldest)
grouper rev. E1565 - without GSM (modern revision)
tilapia rev. E1565 - with GSM



Do I have grouper or tilapia?



TWRP (adb shell) $ grep androidboot.baseband=unknown /proc/cmdline && echo grouper || echo tilapia



Which hardware revision of grouper do I have?





TWRP (adb shell) $ find /sys/devices/ | grep -c max776 && echo You have E1565



TWRP (adb shell) $ find /sys/devices/ | grep -c tps6591 && echo You have PM269



Đặt máy về bootloader, để flash boot.img qua fastboot hoặc dùng method của postmarketOS. Kết nối Nexus 7 vào máy tính qua cáp usb chuẩn 1.0 → 2.0



# sudo adb start-server



# sudo adb reboot bootloader



# sudo fastboot flash boot <boot_filename>.img



Vào TWRP for grouper 3.3.1-0 trở lên https://dl.twrp.me/grouper/

Dùng adb shell trên PC/laptop hoặc Advance/Terminal trong twrp để umount mmcblk0p9 (làm 2 lần cho chắc ăn) [với tilapia (bản 3G) là mmcblk0p10]



1. TWRP(Advance → Terminal): $ df

2. TWRP(Advance → Terminal): $ umount /dev/block/mmcblk0p__  <- fill partition number

3. TWRP(Advance → Terminal): $ umount /dev/block/mmcblk0p__  <- fill partition number



On PC/Laptop terminal:



# adb push <rootfs_filename>.img /dev/block/mmcblk0p__  <- fill partition number



grouper has likely data on /dev/block/mmcblk0p9 but make sure!
tilapia has likely data on /dev/block/mmcblk0p10 but make sure!



Khi cài bản debian pre-image server này vào, debian mặc định sẽ boot vào thẳng root sau lần 2 reboot, khi nhấn Enter tại dòng thông báo



Tạo new user sample như sau:

https://discussions.flightaware.com/t/raspberry-pi-debian-images-latest-release-bullseye/78804/10



# root@rpi4-20210823:~# adduser <username>

Adding user `<username>' ...
Adding new group `<username>' (1001) ...
Adding new user `<username>' (1001) with group `<username>' ...
Creating home directory `/home/<username>' ...
Copying files from `/etc/skel' ...

New password:  ← Nhớ đặt passwd bằng số 0-9

Retype new password:  ← Nhớ đặt passwd bằng số 0-9

passwd: password updated successfully

Changing the user information for <username>

Enter the new value, or press ENTER for the default

Full Name []:
Room Number []:
Work Phone []:
Home Phone []:
Other []:

Is the information correct? [Y/n] y



***Tạo wpa.conf cho kết nối wifi bang wpa_passphrase/wpa_supplicant



# su -



# sudo wpa_passphrase [your_ssid_name] [your_router_passwd] > wpa.conf



Loading wpa.conf vào wpa_supplicant và ping thử google.com



# sudo wpa_supplicant -B -i wlan0 -c wpa.conf



# sudo dhclient wlan0



# sudo ping -c 3 google.com



***Tạo kết nối wifi bang iwd daemon



# sudo apt install iwd



# sudo systemctl enable iwd.service



# sudo systemctl start iwd.service



# sudo iwdctl



[iwd]# device list



[iwd]# station wlan0 scan



[iwd]# station wlan0 get-networks



[iwd]# station wlan0 connect [your_ssid] [your_router_passwd]



[iwd]# exit



# sudo ip a



# sudo ping -c 3 google.com



Cài đặt sudo



# apt-get update



# apt-get install sudo



# exit



login: <username>

passwd: [your_passwd]



Thêm <username> vào groups sudoers



# su -



# usermod -a -G sudo <username>



# groups <username>



<username> : <username> sudo



Update repository



# sudo apt-get update && sudo apt-get upgrade



# sudo apt-get purge --auto-remove raspi-firmware



Cài DE như Lubuntu, Xubuntu, Kubuntu, MATE, GNOME, Phosh, Budgie, Cinamon, Elementary, v.v...



Testing Phoc/Phosh Gnome-shell



# sudo apt-get install phoc phosh phosh-tablet



https://gitlab.gnome.org/World/Phosh/phosh

 Phosh mặc định khởi động vào user id 1000, nên tạo login cho <username>/passwd(bằng số 0-9) đã tạo trước đó



# sudo nano /etc/systemd/system/phosh.service.d/override.conf

[Service]

User=<username>



Cài thêm preload, tlp, tlp-rdw, phosh-tablet, gnome-control-center, network-manager, bluez, nautilus, firefox, chromium, gnome-terminal, gnome-calculator, gnome-clock, gnome-maps, gnome-weather v.v….



# sudo systemctl enable phosh



# sudo systemctl start phosh



Scan fit screen for apps:

https://linmob.net/pinephone-setup-scaling-in-phosh/



Disable systemd service load failed



# sudo systemctl --failed



# sudo systemctl disable networking



# sudo systemctl disable rpi-reconfigure-raspi-firmware



# sudo systemctl disable rpi-set-sysconf



Bypass emergency shell/emergency mod



# sudo systemctl mask emergency.service



# sudo systemctl mask emergency.target



Edit phoc run with xwayland như trang web này nói

https://wayland.freedesktop.org/xserver.html



Cứ xem phoc như weston, edit như sau:



# sudo su -



# sudo nano /usr/share/phosh/phoc.ini



[core]

xwayland = true



[xwayland]

path = /usr/bin/Xwayland



[output:LVDS-1]

modeline = 68.00    800 824 848 880    1280 1285 1286 1318 -hsync +vsync

mode = 800x1280

scale = 1  ← ( 1 or 2 was better than 1.25 - 1.5 - 1.75, because of no gpu accelerate)



# sudo cp /user/share/phosh/phoc.ini /etc/phosh



Voila! Enjoy Gnome-shell Phosh Mobile ;)



https://source.puri.sm/Librem5/community-wiki/-/wikis/Tips-&-Tricks#setting-a-custom-background-in-phosh



https://puri.sm/posts/easy-librem-5-app-development-scale-the-screen/



https://stealthgun.tweakblogs.net/blog/19368/gentoo-on-a-pinephone-making-it-a-usable-phone



Today, I'll show you upgrading to Debian/Mobian bookworm to get latest version of packages



https://blog.mobian-project.org/posts/2021/03/15/unstable-distro/



https://blog.mobian-project.org/posts/2021/05/17/update-2021-05-17/



Get GPG to connect mobian packages:



# sudo gpg --keyserver keyserver.ubuntu.com --search-keys admin@mobian-project.org



# sudo gpg --keyserver keyserver.ubuntu.com --search-keys 951D61F2BC232697



# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 393F924A855FB27D



Create new mobian.list and extrepo_mobian.sources



# sudo nano /etc/apt/sources.list.d/mobian.list



deb http://repo.mobian-project.org/ bookworm main non-free



# sudo nano /etc/apt/sources.list.d/extrepo_mobian.sources



Types: deb deb-src

Architectures: amd64 arm64 armhf

Uris: http://repo.mobian-project.org/

Suites: bookworm

Components: main non-free

Signed-By: /var/lib/extrepo/keys/mobian.asc



# sudo nano /etc/apt/sources.list



deb http://deb.debian.org/debian bookworm main contrib non-free

deb http://security.debian.org/debian-security bookworm-security main contrib non-free

deb http://deb.debian.org/debian bookworm-backports main contrib non-free

deb http://deb.debian.org/debian bookworm-updates main contrib non-free



# sudo apt update && sudo apt upgrade -y



# sudo apt autoclean 0



Voila! Enjoy Gnome-shell Phosh Mobile on Debian/Mobian bookworm ;)



Control cpufreq, vm, kernel parameters



# sudo nano /etc/sysctl.conf



vm.swappiness=15

vm.vfs_cache_pressure=80



# sudo sysctl -p

# sudo chmod +x /opt/cpufreq.start

# sudo chmod +x /opt/temp_throttle



# sudo visudo



ALL ALL=(ALL) NOPASSWD: /opt/cpufreq.start, /opt/temp_throttle



In foreach command create one .desktop at /home/<username>/.config/autostart/



1. sudo /opt/cpufreq.start



2. sudo /opt/temp_throttle 58 <- this is important, because over 60 degrees, n7 will poweroff



mobile-config-firefox



# git clone https://gitlab.com/postmarketOS/mobile-config-firefox.git



# sudo make install



***Backup full filesystem boot and rootfs



Connect Nexus 7 to PC/Laptop using micro-usb cable, enter TWRP recovery mode → Advance → Terminal



# df



On PC/Laptop



# adb start-server



Backup boot: # sudo adb pull /dev/block/mmcblk0p2 /path/to/boot-kernel-5.14-rc3-next-grate.img



Backup rootfs for grouper(wifi): # sudo adb pull /dev/block/mmcblk0p9 /path/to/rootfs.img



Backup rootfs for tilapia(3G): # sudo adb pull /dev/block/mmcblk0p10 /path/to/rootfs.img



Backup full: sudo adb pull /dev/block/mmcblk0 /path/to/full_backup_mmcblk0.img



Image source from raspi.debian.net:

https://raspi.debian.net/verified/20210823_raspi_2_bullseye.img.xz



[MEDIA=youtube]BucRSUBuc50[/MEDIA]
