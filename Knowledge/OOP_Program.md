

OOP (viết tắt của Object Oriented Programming) – lập trình hướng đối tượng là một phương pháp lập trình dựa trên khái niệm về <mark style="background: #FFB86CA6;">lớp và đối tượng</mark>. OOP tập trung vào các đối tượng thao tác hơn là logic để thao tác chúng, giúp code dễ quản lý, tái sử dụng được và dễ bảo trì. OOP là nền tảng của các design pattern hiện nay.

Đọc thêm: [Design pattern là gì? Vì sao nên học design pattern?](https://itviec.com/blog/design-pattern/)

Mục tiêu của OOP là tối ưu việc quản lý source code, giúp tăng khả năng tái sử dụng và quan trọng hơn hết là giúp tóm gọn các thủ tục đã biết trước tính chất thông qua việc sử dụng các đối tượng

# Đối tượng (Object) và Lớp (Class) trong OOP là gì?

### Đối tượng (Object)

Đối tượng <mark style="background: #ADCCFFA6;">gồm 2 thành phần chính</mark> bao gồm: 

- Thuộc tính (Attribute): là những thông tin, đặc điểm của đối tượng.
- Phương thức (Method): là những hành vi mà đối tượng có thể thực hiện

Để dễ hình dung, ta có một ví dụ thực tế về đối tượng là smartphone. Đối tượng này sẽ có:

- Thuộc tính: màu sắc, bộ nhớ, hệ điều hành…
- Phương thức: gọi điện, chụp ảnh, nhắn tin, ghi âm…

### **Lớp (Class)**
Lớp là sự trừu tượng hóa của đối tượng. <mark style="background: #FFB86CA6;">Những đối tượng có những đặc tính tương tự nhau sẽ được tập hợp thành một lớp</mark>. Lớp <mark style="background: #BBFABBA6;">cũng sẽ bao gồm 2 thông tin là thuộc tính và phương thức</mark>.

<mark style="background: #FFB86CA6;">Một đối tượng sẽ được xem là một thực thể của lớp.
</mark>

Tiếp nối ví dụ ở phần đối tượng (object) phía trên, ta có lớp (class) smartphone gồm 2 thành phần:

- Thuộc tính: màu sắc, bộ nhớ, hệ điều hành…
- <mark style="background: #D2B3FFA6;">Phương thức</mark>: gọi điện, chụp ảnh, nhắn tin, ghi âm…

<mark style="background: #FFB8EBA6;">Các đối tượng của lớp này có thể là: iPhone, Samsung, Oppo, Huawei…</mark>

## Ưu điểm của lập trình hướng đối tượng OOP

- OOP <mark style="background: #FFB86CA6;">mô hình hóa những thứ phức tạp</mark> dưới dạng cấu trúc đơn giản.
- Code OOP <mark style="background: #FFB8EBA6;">có thể sử dụng lại</mark>, giúp tiết kiệm tài nguyên.
- Giúp <mark style="background: #D2B3FFA6;">sửa lỗi dễ dàng hơn</mark>. So với việc tìm lỗi ở nhiều vị trí trong code thì tìm lỗi trong các lớp (được cấu trúc từ trước) đơn giản và ít mất thời gian hơn.
- Có <mark style="background: #BBFABBA6;">tính bảo mật cao, bảo vệ thông tin thông qua đóng gói</mark>.
- <mark style="background: #FFB86CA6;">Dễ mở rộng dự án</mark>.


# 4 đặc tính cơ bản của OOP

### Tính đóng gói (Encapsulation)

![[encapsulation_example.png]]


<mark style="background: #FFB86CA6;">Tính đóng gói cho phép che giấu thông tin và những tính chất xử lý bên trong của đối tượng</mark>. Các đối tượng khác không thể tác động trực tiếp đến dữ liệu bên trong và làm thay đổi trạng thái của đối tượng mà bắt buộc phải thông qua các phương thức công khai do đối tượng đó cung cấp.

<mark style="background: #FFB8EBA6;"><mark style="background: #FFB86CA6;">Mục đích</mark>: Tính chất này giúp tăng tính bảo mật cho đối tượng và tránh tình trạng dữ liệu bị hư hỏng ngoài ý muốn</mark>.

Ví dụ: Ta có thể hình dung đến viên thuốc uống. <mark style="background: #ADCCFFA6;">Các thành phần và liều lượng của thuốc sẽ bị các nhà sản suất giấu đi với người dùng và chỉ công khai một số thành phần chính trên vỏ hộp thuốc</mark>. <mark style="background: #FFB86CA6;">Cũng tương ứng việc ta</mark> <mark style="background: #BBFABBA6;">khai báo các lớp với các thuộc tính private</mark>, và để có thể lấy được các dữ liệu của thuộc tính ta sẽ cần sử <mark style="background: #D2B3FFA6;">dụng các phương thức Get() và Set() mà class cung cấp để cập nhập</mark>.



### **Tính kế thừa (Inheritance)**

Đây là tính chất được sử dụng khá nhiều. <mark style="background: #FFB86CA6;">Tính kế thừa cho phép xây dựng một lớp mới (lớp Con), kế thừa và tái sử dụng các thuộc tính, phương thức dựa trên lớp cũ (lớp Cha) đã có trước đó.</mark> 

Các lớp Con kế thừa toàn bộ thành phần của lớp Cha và không cần phải định nghĩa lại. Lớp Con có thể mở rộng các thành phần kế thừa hoặc bổ sung những thành phần mới.

Ví dụ: 

- Lớp Cha là smartphone, có các thuộc tính: màu sắc, bộ nhớ, hệ điều hành…
- Các lớp Con là iPhone, Samsung, Oppo cũng có các thuộc tính: màu sắc, bộ nhớ, hệ điều hành…

![[Inheritance_example.png]]

### **Tính đa hình (Polymorphism)**

Tính đa hình trong lập trình OOP <mark style="background: #FFB86CA6;">cho phép các đối tượng khác nhau thực thi chức năng giống nhau</mark> <mark style="background: #FFB8EBA6;">theo những cách khác nhau.</mark>

<mark style="background: #D2B3FFA6;">Ví dụ:</mark> 
- Chó và mèo cùng nghe mệnh lệnh “kêu đi” từ người chủ. Chó sẽ “gâu gâu” còn mèo lại kêu “meo meo”.

![[Polymorphism_example.png]]


### **Tính trừu tượng (Abstraction)**

Tính trừu tượng giúp<mark style="background: #BBFABBA6;"> loại bỏ những thứ phức tạp, không cần thiết của đối tượng và chỉ tập trung vào những gì cốt lõi, quan trọng</mark>.

Ví dụ: Quản lý nhân viên thì chỉ cần quan tâm đến những thông tin như:

- Họ tên
- Ngày sinh
- Giới tính
- …

Chứ không cần phải quản lý thêm thông tin về:

- Chiều cao
- Cân nặng
- Sở thích
- Màu da
- …

