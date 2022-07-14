
# LAB

###  Mô hình 1 như sau

- Quản lý bridge

| Command | Usage
| ------- | --------------------------
| brctl addbr [namebr] | Tạo bridge
| brctl delbr [namebr] | Xóa bridge
| brctl addif [namebr][nameìf] | Thêm một interface đến bridge
| brctl delif [namebr][nameif] | Xóa một interface trên bridge
| brctl show | Hiển thị danh sách các bridge

- Quản lý STP

| Command | Usage
| ------- | --------------------------
| brctl stp [namebr] on | Bật STP 
| brctl stp [namebr] off | Tắt STP 

![image](https://user-images.githubusercontent.com/83824403/179008688-ad4b4b95-0840-449e-a03e-33450315c6d8.png)



### Bước 1: Tạo switch ảo phucdv. Nếu đã tồn tại có thể xóa switch này đi và tạo lại:

```
brctl delbr phucdv # xóa đi nếu đã tồn tại

brctl addbr phucdv # tạo mới
```

### Bước 2: Gán ens33 vào swicth phucdv

```
brctl addif phucdv ens33

brctl stp phucdv on # enable tính năng STP nếu cần
```

![image](https://user-images.githubusercontent.com/83824403/179002021-0ba050a7-209d-4b4d-bfe1-c9beafc35142.png)


### Bước 3: Khi tạo một switch mới phucdv, trên máy host sẽ xuất hiện thêm 1 NIC ảo trùng tên switch đó (phucdv).

![image](https://user-images.githubusercontent.com/83824403/179004448-9feaaaa3-643d-4055-b850-aaaeff27bb86.png)


Ta có thể cấu hình xin cấp phát IP cho NIC này sử dụng command hoặc cấu hình trong file /etc/network/interfaces để giữ cấu hình cho switch ảo sau khi khởi động lại:

Cấu hình bằng command:
```
ifconfig ens33 0 # xóa IP của ens33

dhclient phucdv
```
Cấu hình trong file /etc/network/interfaces: Nếu trước đó trong file /etc/network/interfaces đã cấu hình cho NIC ens33, ta phải comment lại cấu hình đó hoặc xóa cấu hình đó đi và thay bằng các dòng cấu hình sau:



```
auto phucdv
iface phucdv inet dhcp
bridge_ports phucdv
bridge_stp on
bridge_fd 0
bridge_maxwait 0
```

### Bước 4: Khởi động lại các card mạng và kiểm tra lại cấu hình bridge:

```
ifdown -a && ifup -a # khởi động lại tất cả các NIC

brctl show # kiểm tra cấu hình switch ảo
```







![image](https://user-images.githubusercontent.com/83824403/178963394-b4e35587-a1a7-4bc4-a8d6-ad2775b136d3.png)










- Để check được card `bridge` đã ra được internet chưa thì chúng ta sẽ tắt interface ens37 và inteface ens33 lúc này đã cắm vào bridge `phucdv` nên hiện tại server chỉ còn bridge hoạt động


- ping từ local đến dải của `phucdv`

![image](https://user-images.githubusercontent.com/83824403/179003117-56550750-f37e-4b8f-8328-98dce16a453b.png)

- đứng trên VM chứa bridge `phucdv` chạy lệnh tcpdump để xem các gói tin icmp nhận được chưa

![image](https://user-images.githubusercontent.com/83824403/179003313-868ce7a9-8e29-43bc-9979-9aa7e928bfcb.png)


- Show route table xem có route ra internet qua bridge `phucdv` chưa 


![image](https://user-images.githubusercontent.com/83824403/179003584-0a08d4f0-6856-4a68-af7e-b33e7e49edef.png)

- Traceroute ra internet xem gói tin có đẩy qua GW `172.16.2.2` của bridge `phucdv` chưa

![image](https://user-images.githubusercontent.com/83824403/179003758-17776a65-95f6-4703-ab5b-25c38d4bc92d.png)


- Lúc này các VM kết nối với bridge `phucdv` đã có thể ra internet thoải mái


