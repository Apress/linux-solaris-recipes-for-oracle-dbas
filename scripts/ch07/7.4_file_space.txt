#!/bin/bash
BOX=$(uname -a | awk '{print $2}')
mntlist="/ /boot"
for ml in $mntlist
do
echo "Mount point: $ml"
usedSpc=$(echo $(df -h $ml|awk '{print $5}'|grep -v Use|cut -d "%" -f1 -))
BOX=$(uname -a | awk '{print $2}')
case $usedSpc in
[0-9])
diskStat="relax, lots of disk space: $usedSpc"
;;
[1-7][0-9])
diskStat="disk space okay: $usedSpc"
;;
[8][0-9])
diskStat="space getting low: $usedSpc"
;;
[9][0-9])
diskStat="warning, running out of space: $usedSpc"
echo $diskStat $ml | mailx -s "space on: $BOX" dba@gmail.com
;;
[1][0][0])
diskStat="update resume, no space left: $usedSpc"
echo $diskStat $ml | mailx -s "space on: $BOX" dba@gmail.com
;;
*)
diskStat="huh?: $usedSpc"
esac
# end case
echo $diskStat
done
# end for
exit 0
