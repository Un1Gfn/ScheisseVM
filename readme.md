[Thin provisioning](https://en.wikipedia.org/wiki/Thin_provisioning)
* qcow2
  * https://serverfault.com/a/446044
  * https://forum.proxmox.com/threads/question-about-qocw2-and-thin-provisioning.37605/
  * https://pve.proxmox.com/wiki/Shrink_Qcow2_Disk_Files
  * https://serverfault.com/a/951911 (reverse)

GC w/ fstrim
```bash

# qemu-img(1): Supported image file formats:

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

# mount
# loop
```

GC by creating and deleting a large zero file


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
