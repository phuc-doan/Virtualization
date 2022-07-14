
# 1.Vitualization

-	Virtualization (ảo hóa) là công nghệ được thiết kế để tạo ra tầng trung gian giữa hệ thống phần cứng máy tính và phần mềm chạy trên nó.
- Từ một máy vật lý đơn lẻ có thể tạo thành nhiều máy ảo độc lập. Mỗi một máy ảo đều có một thiết lập nguồn hệ thống riêng, OS và APP riêng

# 2.Vitual Machine 

- Virtual Machine là máy ảo - một môi trường hoạt động độc lập ( phần mềm hoạt động cùng nhưng độc lập với hệ điều hành máy chủ.)

# 3. Hypervisor/VMM

- Hypervisor – Phần mềm giám sát máy ảo: Là một chương trình phần mềm quản lý một hoặc nhiều máy ảo (VM). Nó được sử dụng để tạo, startup, dừng và reset lại các máy ảo. Các hypervisor cho phép mỗi VM hoặc “guest” truy cập vào lớp tài nguyên phần cứng vật lý bên dưới

Tóm lại, hypervisor là các phần mềm công nghệ để tạo máy ảo và giám sát, điều khiển máy ảo. Có 2 loại hypervisor là **Native** ( hay còn gọi là Bare metal ) và **Host Based**.


![image](https://user-images.githubusercontent.com/83824403/178897243-bec0de1e-72dc-4976-bc82-25f9032ca7e6.png)




### Native

-	Một hypervisor ở dạng **native** (hay còn gọi “bare-metal”) chạy trực tiếp trên phần cứng. Nó nằm giữa phần cứng và một hoặc nhiều hệ điều hành khách (guest operating system).
-	Nó được khởi động trước cả hệ điều hành và tương tác trực tiếp với kernel. Performance cao vì không có OS chính nào cạnh tranh tài nguyên với nó.
-	Tuy nhiên, nó cũng đồng nghĩa với việc hệ thống chỉ có thể được sử dụng để chạy các máy ảo vì hypervisor luôn phải chạy ngầm bên dưới.
-	Các hypervisor dạng native này có thể kể đến như **VMware ESXi**, **Microsoft Hyper-V** và **Apple Boot Camp**.

### Hosted

-	Một hypervisor dạng hosted được cài đặt ở node vật lý (host computer), mà trong đó node vật lý đã có OS chạy sẵn.
-	Nó chạy như một ứng dụng cũng như các phần mềm trên node vật lý
- Hầu hết các hypervisor dạng hosted có thể quản lý và chạy nhiều máy ảo cùng một lúc. Lợi thế của một hypervisor dạng hosted là nó có thể được bật lên hoặc thoát ra khi cần thiết, giải phóng tài nguyên cho máy chủ.
- Tuy nhiên, vì chạy bên trên một hệ điều hành, nó không thể đem lại hiệu suất tương tự như một hypervisor ở dạng native.
- Ví dụ về các hypervisor dạng hosted bao gồm **VMware Workstation**, **Oracle VirtualBox** và **Parallels Desktop for Mac**.
