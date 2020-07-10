# Gigaset GS185

## collecting Information
- kernel sources: https://www.gigaset.com/de_de/cms/lp/open-source.html
    * at least a git repo
    * but so many untracked and unstaged files...
- key combos:
    * Volume up + power = bootloader/fastboot (red blinking LED when connected to PC)
    * Volume down + power = recovery
- by enabling adb some info can already be accessed
    * `adb shell getprop`
- bootloader is unlockable
    * enable developer options by clicking buildnumber 7 times
    * enable OEM unlocking in developer options
    * get into bootloader
    * `fastboot oem unlock-go`
    * userdata will be wiped
- partition info and system dump requires root or custom recovery
- Gigaset doesn't provide update packages for downloading (only OTA)
- we need information included in boot.img so we somehow need to intercept it
    * seems I have missed the chance...
    * get your logcat while searching the new OTA, it could include the address to download
    * doing so after already downloaded and installed won't release any secrets
- as our device is 64 bit A-only treble compatible we can also use a GSI (more info https://github.com/phhusson/treble_experimentations/wiki)
- flash a suitable image with `fastboot flash system image.img`
- for me it sadly seemed as it hangs in a bootloop BUT adb is running and also root access can be gained
- collect partition information
    * `cat /proc/partitions`
    ```
    major minor  #blocks  name
    179        0   15267840 mmcblk0
    179        1      86016 mmcblk0p1
    179        2          1 mmcblk0p2
    179        3          8 mmcblk0p3
    179        4        512 mmcblk0p4
    179        5        512 mmcblk0p5
    179        6        512 mmcblk0p6
    179        7        512 mmcblk0p7
    179        8       2048 mmcblk0p8
    179        9       2048 mmcblk0p9
    179       10        256 mmcblk0p10
    179       11        256 mmcblk0p11
    179       12      16384 mmcblk0p12
    179       13       1536 mmcblk0p13
    179       14       1536 mmcblk0p14
    179       15         32 mmcblk0p15
    179       16       1536 mmcblk0p16
    179       17         16 mmcblk0p17
    179       18      11264 mmcblk0p18
    179       19       1024 mmcblk0p19
    179       20       1024 mmcblk0p20
    179       21      65536 mmcblk0p21
    179       22      65536 mmcblk0p22
    179       23       1024 mmcblk0p23
    179       24    3145728 mmcblk0p24
    179       25    1048576 mmcblk0p25
    179       26     262144 mmcblk0p26
    179       27      32768 mmcblk0p27
    179       28      10240 mmcblk0p28
    179       29       1024 mmcblk0p29
    179       30        512 mmcblk0p30
    179       31         32 mmcblk0p31
    259        0     262144 mmcblk0p32
    259        1         32 mmcblk0p33
    259        2        512 mmcblk0p34
    259        3       1024 mmcblk0p35
    259        4      32768 mmcblk0p36
    259        5        512 mmcblk0p37
    259        6       4096 mmcblk0p38
    259        7       1024 mmcblk0p39
    259        8       1024 mmcblk0p40
    259        9       1024 mmcblk0p41
    259       10       1024 mmcblk0p42
    259       11       1024 mmcblk0p43
    259       12       1024 mmcblk0p44
    259       13        256 mmcblk0p45
    259       14        256 mmcblk0p46
    259       15          8 mmcblk0p47
    259       16      65536 mmcblk0p48
    259       17    9631207 mmcblk0p49
    179       32       4096 mmcblk0rpmb
    253        0    9631191 dm-0
    ```
    * `ls -l /dev/block/platform/soc/7824900.sdhci/by-name`
    ```
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 DDR -> /dev/block/mmcblk0p15
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 aboot -> /dev/block/mmcblk0p19
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 abootbak -> /dev/block/mmcblk0p20
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 apdp -> /dev/block/mmcblk0p45
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 boot -> /dev/block/mmcblk0p21
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 cache -> /dev/block/mmcblk0p26
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 cmnlib -> /dev/block/mmcblk0p39
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 cmnlib64 -> /dev/block/mmcblk0p41
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 cmnlib64bak -> /dev/block/mmcblk0p42
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 cmnlibbak -> /dev/block/mmcblk0p40
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 config -> /dev/block/mmcblk0p31
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 devcfg -> /dev/block/mmcblk0p10
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 devcfgbak -> /dev/block/mmcblk0p11
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 devinfo -> /dev/block/mmcblk0p23
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 dip -> /dev/block/mmcblk0p35
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 dpo -> /dev/block/mmcblk0p47
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 dsp -> /dev/block/mmcblk0p12
    lrwxrwxrwx 1 root root 20 1970-01-01 00:04 fsc -> /dev/block/mmcblk0p2
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 fsg -> /dev/block/mmcblk0p16
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 keymaster -> /dev/block/mmcblk0p43
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 keymasterbak -> /dev/block/mmcblk0p44
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 keystore -> /dev/block/mmcblk0p30
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 limits -> /dev/block/mmcblk0p33
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 logdump -> /dev/block/mmcblk0p48
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 mcfg -> /dev/block/mmcblk0p38
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 mdtp -> /dev/block/mmcblk0p36
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 misc -> /dev/block/mmcblk0p29
    lrwxrwxrwx 1 root root 20 1970-01-01 00:04 modem -> /dev/block/mmcblk0p1
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 modemst1 -> /dev/block/mmcblk0p13
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 modemst2 -> /dev/block/mmcblk0p14
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 mota -> /dev/block/mmcblk0p34
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 msadp -> /dev/block/mmcblk0p46
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 oem -> /dev/block/mmcblk0p32
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 persist -> /dev/block/mmcblk0p27
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 proinfo -> /dev/block/mmcblk0p28
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 recovery -> /dev/block/mmcblk0p22
    lrwxrwxrwx 1 root root 20 1970-01-01 00:04 rpm -> /dev/block/mmcblk0p6
    lrwxrwxrwx 1 root root 20 1970-01-01 00:04 rpmbak -> /dev/block/mmcblk0p7
    lrwxrwxrwx 1 root root 20 1970-01-01 00:04 sbl1 -> /dev/block/mmcblk0p4
    lrwxrwxrwx 1 root root 20 1970-01-01 00:04 sbl1bak -> /dev/block/mmcblk0p5
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 sec -> /dev/block/mmcblk0p17
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 splash -> /dev/block/mmcblk0p18
    lrwxrwxrwx 1 root root 20 1970-01-01 00:04 ssd -> /dev/block/mmcblk0p3
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 syscfg -> /dev/block/mmcblk0p37
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 system -> /dev/block/mmcblk0p24
    lrwxrwxrwx 1 root root 20 1970-01-01 00:04 tz -> /dev/block/mmcblk0p8
    lrwxrwxrwx 1 root root 20 1970-01-01 00:04 tzbak -> /dev/block/mmcblk0p9
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 userdata -> /dev/block/mmcblk0p49
    lrwxrwxrwx 1 root root 21 1970-01-01 00:04 vendor -> /dev/block/mmcblk0p25
    ```
- use these information to dump important partitions (you can also backup all), most important for TWRP bringup:
    * boot `dd if=/dev/block/mmcblk0p21 of=/cache/boot.img`
    * recovery `dd if=/dev/block/mmcblk0p22 of=/cache/recovery.img`
- pull them via adb
    * boot `adb pull /cache/boot.img`
    * recovery `adb pull /cache/recovery.img`
- analyse them with AIK (https://forum.xda-developers.com/showthread.php?t=2073775)
## building TWRP
- setup basic makefile skeleton with architecture and kernel definitions like offsets, kernel cmd-line... (gathered from recovery image)
- official build guide: https://forum.xda-developers.com/showthread.php?p=32965365#post32965365
- remove annoying cursor with `TW_INPUT_BLACKLIST := "hbtp_vm"`
- set partition sizes by using `cat /proc/partitions`
- flash built TWRP with `fastboot flash recovery revovery.img`
- pull and analyse log `adb pull /tmp/recovery.log`
    * mounting still broken: need to fix fstab
    * */dev/block/by-name/boot* instead of */dev/block/**bootdevice**/by-name/boot*
- now that we have full access let's dump all partitions
    * system, cache and userdata can be omitted
    * temporary store the images on */tmp*
    * need to backup in two steps as */tmp* can only hold ~1GB
    * ```
        #!/bin/sh
        i=0

        while [ $i -lt 49 ]
        do
            echo /dev/block/mmcblk0p$i
            dd if=/dev/block/mmcblk0p$i of=/tmp/mmcblk0p$i.img
            i=`expr $i + 1`
        done
        ```
    * copy to host `adb pull /tmp`
## building LineageOS 