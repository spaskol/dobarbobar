--this is for ADB android making dumb file on the storage --

apt-get install testdisk pv extundelete

adb shell su -c "cat /dev/block/platform/msm_sdcc.1/by-name/userdata" | pv > /home/yoga/userdata.raw

adb shell su -c "cat /dev/block/mmcblk0" | pv > mmcblk0.raw

https://recoverit.wondershare.com/file-recovery/android-data-recovery-linux.html 