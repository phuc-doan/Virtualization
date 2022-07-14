## 1 Install KVM Ubuntu 20.04

- Kiểm tra tính năng VT ( Virtualization Technology ) đã được enable chưa, nếu chưa thì có thể enable trong BIOS :


![image](https://user-images.githubusercontent.com/83824403/178930721-0e96f007-351f-4af0-bf7f-fea08f238dbe.png)




- Kiểm tra Hardware có hỗ trợ ảo hóa không, nếu lệnh sau trả về = 0 thì không hỗ trợ :

```
egrep -c "svm|vmx" /proc/cpuinfo
2

```

- Cài đặt các gói sau và enable chúng :

```
sudo -y apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst virt-manager
systemctl enable --now libvirtd
```

## 2. Một vài lệnh với virsh


```
man virsh
virsh -h
```

![image](https://user-images.githubusercontent.com/83824403/178931582-eeaf2952-b568-4735-9c1b-b36d3861a157.png)



- Một số command phổ biến:


virsh list  : list all vm đang bật

virsh list --all  : list all vm in node

virsh start [name]  : khởi động vm [name]

virsh autostart [name]  : khởi động vm chỉ định cùng hệ thống

virsh dominfo [name]  : check info vm

virsh suspend/resume [name]  : pause/resume vm

virsh destroy [name] : force shutdown vm

virsh undefine [name]  : xóa vm

virsh edit [name] : edit vm

virsh dumpxml [name] > name.xml : dump file xml của vm

virsh define name.xml  : tạo vm từ file xml




## 3. Thao tác

- Tạo 1 VM từ virsh command

```virt-install --virt-type=kvm --os-type=Linux --name centos --ram 1024 --vcpus=1 --os-variant=centos7.0 --cdrom=/home/phucdv/CentOS-7-x86_64-Minimal-2009.iso  --network=bridge=virbr0,model=virtio --disk path=/var/lib/libvirt/images/centos7.qcow2,size=10


- Trong đó, các option có ý nghĩa :


--virt-type : loại ảo hóa

--os-type : loại OS

--name : tên VM

--ram : ram

--vcpus : lượng vcpus cấp cho VM

--os-variant : chọn biến thế của os, để lấy giá trị osinfo-query os

--cdrom: Chọn nơi chứa image cài đặt (iso, qcow2,...)

--network: setup network

--disk path: : cấu hình hard disk cho vm, size = 10GB.
