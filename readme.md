[libgusetfs](http://libguestfs.org/)
([Wikipedia](https://en.wikipedia.org/wiki/Libguestfs))
([GitHub](https://github.com/libguestfs/libguestfs))
([AUR](https://aur.archlinux.org/packages/libguestfs/))

[Thin provisioning](https://en.wikipedia.org/wiki/Thin_provisioning)
* qcow2
  * https://askubuntu.com/questions/391101/does-trim-work-with-fat32
  * https://serverfault.com/a/446044
  * https://forum.proxmox.com/threads/question-about-qocw2-and-thin-provisioning.37605/
  * https://pve.proxmox.com/wiki/Shrink_Qcow2_Disk_Files
  * https://serverfault.com/a/951911 (reverse)

Create qcow2 image

```bash
# create [--object OBJECTDEF] [-q] [-f FMT] [-b BACKING_FILE] [-F BACKING_FMT] [-u] [-o OPTIONS] FILENAME [SIZE]
sh -c "
echo
rm -fv tmp.qcow2
echo
qemu-img create -f qcow2 tmp.qcow2 1T
echo
qemu-img  info tmp.qcow2
echo
qemu-img check -f qcow2 tmp.qcow2
echo
ls -lh tmp.qcow2
echo
"
```

Connection/disconnect

```bash
modprobe nbd
lsmod | grep nbd
alias disconn='echo -ne "\n  Umounted? "; read -r; echo; sudo qemu-nbd -v -d /dev/nbd0'
alias conn='disconn; sudo qemu-nbd -v --discard=unmap --detect-zeroes=unmap -c /dev/nbd0 tmp.qcow2 &'
```

EXT4 w/ deleted garbage

```bash
conn

echo -e "o\nn\n\n\n\n\nw\n\n\n\n" | fdisk /dev/nbd0
sync;partprobe;sleep 3
mkfs.ext4 /dev/nbd0p1
sync;partprobe;sleep 3
mount -v /dev/nbd0p1 /mnt
dd if=/dev/urandom of=/mnt/garbage count=$((2*1024*1024)) status=progress
sync
rm -v /mnt/garbage
umount /mnt

disconn
```

EXT4 GC w/ fstrim and noop conversion

```bash
conn

mount /dev/nbd0p1 /mnt
fstrim -av

disconn

qemu-img convert -O qcow2 tmp.qcow2 shrunk.qcow2
ls -lh *.qcow2
# -rw-r--r-- 1 root root  81M May 16 21:20 shrunk.qcow2
# -rw-r--r-- 1 root root 2.2G May 16 21:20 tmp.qcow2
```

FAT32 w/ deleted garbage

```bash
conn

echo -e "o\nn\n\n\n\n\nt\nb\nw\n\n\n\n" | fdisk /dev/nbd0
sync;partprobe;sleep 3
mkfs.fat -v /dev/nbd0p1
sync;partprobe;sleep 3
ls -lh
mount -v /dev/nbd0p1 /mnt
dd if=/dev/urandom of=/mnt/garbage count=$((2*1024*1024)) status=progress
sync
rm -v /mnt/garbage
umount /mnt

disconn
```

FAT32 GC (by creating and deleting a large zero file?)

```bash

conn
mount -o discard /dev/nbd0p1 /mnt
pushd /mnt

i=0
SZ="$((1024*1024*1024*4-1))"
while true; do
  F="zerofill_$i"
  echo "$F"
  # touch "$F"; fallocate -z -l $((1024*1024*1024*4-1)) -v "$F"
  truncate -s "$SZ" "$F"
  X="$?"
  [ "$X" -eq 0 ] || break;
  i=$((i+1))
done

rm garbage_*

popd
disconn

qemu-img convert -O qcow2 tmp.qcow2 shrunk.qcow2

```



[FreeDOS](https://www.freedos.org/)
* [download](https://www.freedos.org/download/)

```bash
chmod -w *.iso
chmod -w *.ISO
chmod -w *.zip
chmod -w *_base.qcow2
```

Extract file from ISO
* https://superuser.com/questions/180744/how-do-i-extract-an-iso-on-linux-without-root-access
* https://blog.sleeplessbeastie.eu/2014/08/26/how-to-extract-an-iso-image/
```bash
ISO="/home/darren/qemu/FD13LIVE.ISO"
mkdir -v PING; pushd PING
isoinfo -i "$ISO" -x '/NET/PING.ZIP;1' >tmp.zip
unzip tmp.zip
rm -v tmp.zip
popd
```




<details><summary>&nbsp;</summary>

Thin provisioning VDI
  * https://superuser.com/a/529183/)
  * https://www.howtogeek.com/312883/how-to-shrink-a-virtualbox-virtual-machine-and-free-up-disk-space/)

VirtualBox
  * [Create VM](https://www.andreafortuna.org/2019/10/24/how-to-create-a-virtualbox-vm-from-command-line/)

Convert between raw, qcow2, qed, vdi, vmdk, vhd
  * [openstack](https://docs.openstack.org/image-guide/convert-images.html)
  * [superuser](https://superuser.com/questions/360517)


</details>
