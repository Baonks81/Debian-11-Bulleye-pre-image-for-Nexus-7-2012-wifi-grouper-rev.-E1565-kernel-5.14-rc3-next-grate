# Debian-11-Bulleye-pre-image-for-Nexus-7-2012-wifi-grouper-rev.-E1565-kernel-5.14-rc3-next-grate
Linux on Nexus 7 2012

Link trên Google drive:

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



$ sudo adb start-server



$ sudo adb reboot bootloader



$ sudo fastboot flash boot <boot_filename>.img



Vào TWRP for grouper 3.3.1-0 trở lên https://dl.twrp.me/grouper/

Dùng adb shell trên PC/laptop hoặc Advance/Terminal trong twrp để umount mmcblk0p9 (làm 2 lần cho chắc ăn) [với tilapia (bản 3G) là mmcblk0p10]



1. TWRP(Advance → Terminal): $ df

2. TWRP(Advance → Terminal): $ umount /dev/block/mmcblk0p__  <- fill partition number

3. TWRP(Advance → Terminal): $ umount /dev/block/mmcblk0p__  <- fill partition number



On PC/Laptop terminal:



$ adb push <rootfs_filename>.img /dev/block/mmcblk0p__  <- fill partition number



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



Tạo wpa.conf cho kết nối wifi



# su -



# sudo wpa_passphrase [your_ssid_name] [your_router_passwd] > wpa.conf



Loading wpa.conf vào wpa_supplicant và ping thử google.com



# sudo wpa_supplicant -B -i wlan0 -c wpa.conf



# sudo dhclient wlan0



# sudo ping -c 3 google.com



# sudo apt-get update && sudo apt-get upgrade



# sudo apt-get purge --auto-remove raspi-firmware



# sudo apt-get install phoc phosh



https://github.com/agx/phosh

 Phosh mặc định khởi động vào user id 1000, nên tạo login cho <username>/passwd(bằng số 0-9) đã tạo trước đó



# sudo nano /etc/systemd/system/phosh.service.d/override.conf

[Service]

User=<username>



Cài thêm gnome-control-center, network-manager, bluez, nautilus, firefox, chromium, gnome-terminal, gnome-software, gnome-maps, gnome-weather v.v….



# sudo systemctl enable phosh



# sudo systemctl start phosh



Scan fit screen for apps:

https://linmob.net/pinephone-setup-scaling-in-phosh/



Voila! Enjoy Gnome-shell Phosh Mobile ;)
