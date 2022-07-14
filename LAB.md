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

```
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
```



## 3. Thao tác

- Tạo 1 VM từ virsh command

```
virt-install --virt-type=kvm --os-type=Linux --name centos --ram 1024 --vcpus=1 --os-variant=centos7.0 --cdrom=/home/phucdv/CentOS-7-x86_64-Minimal-2009.iso  --network=bridge=virbr0,model=virtio --disk path=/var/lib/libvirt/images/centos7.qcow2,size=10
```

- Trong đó, các option có ý nghĩa :

```
--virt-type : loại ảo hóa
--os-type : loại OS
--name : tên VM
--ram : ram
--vcpus : lượng vcpus cấp cho VM
--os-variant : chọn biến thế của os, để lấy giá trị osinfo-query os
--cdrom: Chọn nơi chứa image cài đặt (iso, qcow2,...)
--network: setup network
--disk path: : cấu hình hard disk cho vm, size = 10GB.
```






## 4. CPU pinning :

- Liên kết VCPU của VM với CPU vật lý. Tận dụng hiệu năng của CPU vật lý tốt hơn cho CPU ảo của VM.
**Show vcpupin :**

```
virsh # vcpupin centos7 
 VCPU   CPU Affinity
----------------------
 0      0-3
 1      0-3

```

- Thực hiện pinning CPU ( cú pháp sau pinning Vcpu ID 0 -> Physical CPU ID 0, Vcpu ID 1 -> Physical CPU ID 1 )

**
virsh # vcpupin centos7 0 0
virsh # vcpupin centos7 1 1
**


- Kiểm tra lại :

```
virsh # vcpupin centos7 
 VCPU   CPU Affinity
----------------------
 0      0
 1      1
```


## 5. limit CPU

- listing info :

```
virsh # vcpucount centos7 
maximum      config         2
maximum      live           2
current      config         2
current      live           2
```


- Trong đó :

+ maximum config : chỉ định số Vcpus tối đa có thể được cấp cho vm sau lần restart tiếp theo
+ maximum live : chỉ định số Vpcpus tối đa có thể được cấp cho vm đang chạy/pause
+ current config : chỉ định số Vcpus được cấp phát cho vm sau lần restart tiếp theo.
+ current live : chỉ định số Vcpus được cấp phát cho vm đang chạy/pause.


- Đối với config live, cpu cần hỗ trợ cơ chế hotplug thì mới config được.

-  limit cpu :

```
virsh setvcpus <VM-name> <max-number-of-CPUs> [option]
```


- thêm các arg tương ứng với các trường hợp muốn thay đổi config :

```
--config : thay đổi current config

--config --maximum: thay đổi current và maximum

--live : thay đổi live
```



## 6. limit bandwidth :

Để limit bw, ta thêm phần config bandwidth vào interface trong file cấu hình xml của VM, cụ thể như sau :

- vào thay đổi cấu hình : virsh edit centos7

- tìm phần cấu hình interfaces thay đổi như sau, giới hạn tốc độ up/down ~ 100KB/s  :




<interface type='bridge'>
      <mac address='52:54:00:cd:1f:44'/>
      <source bridge='virbr0'/>
      <bandwidth>
        <inbound average='100' peak='100' burst='100'/>
        <outbound average='100' peak='1000' burst='100'/>
      </bandwidth>
      <model type='virtio'/>
      <address type='pci' domain='0x0000' bus='0x01' slot='0x00' function='0x0'/>
    </interface>



- Kiểm tra lại bằng speedtest :

```
root@localhost ~]# speedtest

   Speedtest by Ookla

     Server: ZHOST.VN - Ha Noi (id = 34705)
        ISP: FPT Telecom
    Latency:     1.96 ms   (1.18 ms jitter)
   Download:     0.76 Mbps (data used: 364.0 kB)                               
     Upload:     0.77 Mbps (data used: 1.1 MB)                               
Packet Loss:     0.0%
 Result URL: https://www.speedtest.net/result/c/fbba13ef-9a09-47c9-9172-cab626123c22
 ```



## 7. NIC :

- listing interface :

```
tule@tule:~$ virsh domiflist centos7 
 Interface   Type     Source   Model    MAC
-----------------------------------------------------------
 vnet0       bridge   virbr0   virtio   52:54:00:cd:1f:44

```

- add NIC to VM :

```
virsh attach-interface --domain [tên VM] --type [loại card mạng] --source [tên card mạng] --model [model card mạng] --mac [địa chỉ mac] --config --live
```


- remove NIC :

```
virsh detach-interface --domain [tên VM] --type [loại card mạng] --mac [địa chỉ mac] --config --live
```


## 8. Volume :

- Listing disk :

```
tule@tule:~$ virsh domblklist centos7 
 Target   Source
-------------------------------------------------
 vda      /var/lib/libvirt/images/centos7.qcow2
 sda      -

```

- Create new disk ( tạo ổ format  với dung lượng 1GB ) :

```
cd /var/lib/libvirt/images 
virsh vol-create-as default disk1 1GB --format qcow2
or
qemu-img create -f qcow2 disk1 1GB 

```

- kiểm tra disk vừa tạo :

```
qemu-img info disk1
```



- Convert disk sang định dạng raw :

```
qemu-img convert -f qcow2 -O  raw disk1 disk1.img

```

- Resize disk :

```
qemu-img resize -f qcow2 disk1 2G

```

- Add disk to VM :

```
virsh attach-disk centos7 --source /var/lib/libvirt/images/disk1 --target vdb --persistent
```


- Remove disk from VM :


```
virsh detach-disk centos7 /var/lib/libvirt/images/disk1 --persistent --config --live
```


## 9. Migrate :

- command :

```
# virsh migrate [option] [DomainName] [DestinationURL]\
```

- Example :

MIgrate offline : virsh migrate --offline --persistent centos7 quemu+ssh://virsh2@192.168.122.232/system

Migrate live : virsh migrate --live centos7 quemu+ssh://virsh2@192.168.122.232/system
