# 1. Lí thuyết Linux-bridge


### 1.1. Khái niệm
Linux bridge là một phần mềm đươc tích hợp vào trong nhân Linux để giải quyết vấn đề ảo hóa phần network trong các máy vật lý. Về mặt logic Linux bridge sẽ tạo ra một con switch ảo để cho các VM kết nối được vào và có thể nói chuyện được với nhau cũng như sử dụng để ra mạng ngoài

Ngoài ra khi tìm hiểu Linux bridge còn có một số thuật ngữ như:
- Port : tương tự như cổng của một con switch thật
- Bridge: tương đương với từ switch
- tap : được hiểu ở lớp 2
- FDB : Forwading database
<a name="kt"></a>
### 1.2. Kiến trúc



![image](https://user-images.githubusercontent.com/83824403/178925685-f35eb369-8aec-4f63-8ab2-a0a01d375814.png)



Kiến trúc của Linux bridge như hình sau:

<img class="image__pic js-image-pic" src="http://i.imgur.com/xob7ljQ.png" alt="" id="screenshot-image">

- Bridge ở đây là switch ảo
- Tap hay tap interface là giao diện mạng để các VM kết nối với switch do Linux bridge tạo ra(nó hoạt động ở lớp 2 của mô hình OSI)
- fd: Forward data có nhiệm vụ chuyển dữ kiệu từ VM tới switch.
<a name="tp"></a>


### 1.3. Chức năng của một switch ảo do Linux bridge tạo ra
- STP: là tính năng chống loop gói tin trong switch
- VLan: là tính năng rất quan trọng trong một switch
- FDB: là tính năng chuyển gói tin theo database được xây dựng giúp tăng tốc độ của switch

<a name="lab"></a>



# 2. LAB

- Mô hình như sau

![image](https://user-images.githubusercontent.com/83824403/178926109-e2932395-906e-4d03-b9fc-c16bc0f1e7e7.png)

