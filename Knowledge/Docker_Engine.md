
Docker Engine is an open source containerization technology for building and containerizing your applications. Docker Engine acts as a client-server application with:

- A server with a long-running daemon process [`dockerd`](https://docs.docker.com/engine/reference/commandline/dockerd).
- APIs which specify interfaces that programs can use to talk to and instruct the Docker daemon.
- A command line interface (CLI) client [`docker`](https://docs.docker.com/engine/reference/commandline/cli/).

The CLI uses [Docker APIs](https://docs.docker.com/engine/api/) to control or interact with the Docker daemon through scripting or direct CLI commands. Many other Docker applications use the underlying API and CLI. The daemon creates and manage Docker objects, such as images, containers, networks, and volumes.


# Khác biệt giữa Docker Engine & Docker Desktop

<mark style="background: #FFB86CA6;">Docker Engine và Docker Desktop là hai thành phần chính của Docker</mark>, nhưng chúng có một số sự khác biệt quan trọng.

**Docker Engine:**
- <mark style="background: #BBFABBA6;">Docker Engine là nền tảng chạy các container Docker</mark>. Đây là thành phần cơ bản của Docker, <mark style="background: #BBFABBA6;">cung cấp môi trường thực thi cho việc chạy và quản lý các container.</mark>

- Docker Engine <mark style="background: #D2B3FFA6;">chủ yếu tập trung vào việc cung cấp dòng lệnh và API để tương tác với các container.</mark> Nó không có giao diện người dùng đồ họa (GUI).


**Docker Desktop:**
- <mark style="background: #FFB8EBA6;">Docker Desktop là một ứng dụng cài đặt trên máy tính cá nhân của bạn. Nó bao gồm Docker Engine, giao diện người dùng đồ họa (GUI)</mark> để quản lý container, và một số công cụ hỗ trợ khác như Docker Compose.

- Docker Desktop thường được <mark style="background: #ADCCFFA6;">sử dụng cho môi trường phát triển (development) và kiểm thử.</mark> Nó cung cấp một cách thuận tiện để tạo, <mark style="background: #ADCCFFA6;">quản lý và theo dõi container mà không cần sử dụng dòng lệnh.</mark>

Vì vậy, <mark style="background: #FFB86CA6;">Docker Engine là phần chính để chạy container, trong khi Docker Desktop là một gói cài đặt chứa Docker Engine và một số công cụ giúp đỡ, đặc biệt là với những người phát triển muốn sử dụng giao diện đồ họa.</mark> Trên môi trường sản xuất, Docker Engine có thể được triển khai trực tiếp trên các máy chủ mà không cần Docker Desktop.


# Manage data in Docker

Theo <mark style="background: #FFB86CA6;">mặc định tất cả tệp dược tạo bên trong bùng chứa đều được lưu trữ trên lớp vùng chứa có thể ghi</mark>. Điêu này dẫn đến:

- <mark style="background: #ADCCFFA6;">Dữ liệu không tồn tại khi container đó không còn tồn tại và  có thể khó lấy dữ liệu ra khỏi container nếu một tiến trình khác cần nó.</mark>

- lớp có thể ghi của container's được liên kết chặt chẽ với máy chủ nơi container đang chạy. Ta <mark style="background: #BBFABBA6;">không thể dễ dàng di chuyển dữ liệu đi nơi khác</mark>.

- Việc ghi vào lớp có thể ghi (container's writable layer) yêu cầu trình diều khiển lưu trữ ( [storage driver](https://docs.docker.com/storage/storagedriver/))  để quản lý hệ thống tệp. Trình điều khiển này cung cấp một hệ thống tập tin, sử dụng nhân Linux.  <mark style="background: #ADCCFFA6;">Việc trừu tượng hóa thêm này sẽ làm giảm hiệu suất so với việc sử dụng trình ghi/dọc trực tếp vào hệ thống tệp máy chủ.</mark>

Docker <mark style="background: #FFB8EBA6;">có 2 lựa chọn vùng chứa (containers) để lưu trữ các file trên máy ch</mark>ủ (host machine), do đó các file dữ liệu sẽ được duy trì ngay cả khi các container dừng lại đó là:
- <mark style="background: #D2B3FFA6;">Volumes và Bind mounts.</mark>


Ngoài ra docker còn <mark style="background: #BBFABBA6;">hỗ trợ các containers lưu trữ các tập tin trong bộ nhớ trên máy chủ tuy nhiên chúng sẽ được đặt dưới dạng gói tin tạm</mark> ([temporary file](Temporary_File.md)).<mark style="background: #FFB86CA6;"> Nếu ta đang chạy Docker trên Linux, `tmpfs` mount được sử dụng để lưu trữ các tệp trong bộ nhớ hệ thống của máy chủ. Nếu ta đang chạy Docker trên Windows, ống có tên được sử dụng để lưu trữ tệp trong bộ nhớ hệ thống của máy chủ.</mark>

## [Choose the right type of mount](https://docs.docker.com/storage/#choose-the-right-type-of-mount)

Cho dù bạn chọn sử dụng loại mount nào, <mark style="background: #FFB8EBA6;">dữ liệu sẽ đều trông giống nhau từ bên trong container. Nó được hiển thị dưới dạng một thư mục hoặc một tệp riêng lẻ trong hệ thống tệp của container.</mark>

"An easy way to visualize the difference among volumes, bind mounts, and `tmpfs` mounts is to think about where the data lives on the Docker host."

Một cách dễ dàng để hình dung <mark style="background: #ADCCFFA6;">sự khác biệt giữa các volumes, bind mounts và tmpfs mounts</mark> là suy nghĩ về <mark style="background: #FFB86CA6;">vị trí của dữ liệu trên máy chủ Docker</mark>.

![Types of mounts and where they live on the Docker host](https://docs.docker.com/storage/images/types-of-mounts.webp?w=450&h=300)

- Volumes are stored in a part of the host filesystem which is _managed by Docker_ (`/var/lib/docker/volumes/` on Linux). <mark style="background: #FFB86CA6;">Non-Docker processes should not modify this part of the filesystem</mark>. Volumes are the best way to persist data in Docker.
    
- Bind mounts <mark style="background: #D2B3FFA6;">may be stored anywhere on the host system</mark>. They may even be important system files or directories. <mark style="background: #D2B3FFA6;">Non-Docker processes on the Docker host or a Docker container can modify them at any time</mark>.
    
- `tmpfs` mounts are <mark style="background: #FFB8EBA6;">stored in the host system's memory only, and are never written to the host system's filesystem.</mark> This files will be remove if container reset.


Bind mounts and volumes can both be mounted into containers using the `-v` or `--volume` flag, but the syntax for each is slightly different. For `tmpfs` mounts, you can use the `--tmpfs` flag. We <mark style="background: #BBFABBA6;">recommend using the `--mount` flag for both containers and services, for bind mounts, volumes, or `tmpfs` mounts, as the syntax is more clear.</mark>


## [Good use cases for volumes](https://docs.docker.com/storage/#good-use-cases-for-volumes)

Volumes làm một cách ưu tiên để lưu trữ dữ liệu trong Docker containers hay trong services. Sau đây tôi sẽ liệt kê một số trường hợp ta sử dụng volumes: 


- **Chia sẻ dữ liệu giữa nhiều Containers đang chạy**:

	- Nếu ta không tạo một ổ đĩa ([Volumes](Docker_volume.md)) một cách rỗ ráng, thì <mark style="background: #ADCCFFA6;">mặc định Container sẽ tự được găn với một Volume. Tuy nhiên khi Container dừng hoặc bị xoá ổ đĩa (Volumes) sẽ vẫn tồn tại.</mark> 

	- <mark style="background: #D2B3FFA6;">Điều này dẫn đến việc Container có thể dẫn đến đến việc các Container có thể được gắn với nhiều ổ đĩa cùng một lúc và tạo ra một số trường hợp lỗi ngoại lệ khi đọc ghi dữ liệu</mark>. Ngoài ra các ổ đĩa ([Volumes](Docker_volume.md)) mặc định này chỉ bị xoá bằng cách thủ công. Có nghĩa là<mark style="background: #FFB8EBA6;"> bạn bắt buộc phải gõ từ câu lệnh CLI của Docker để xoá chúng một cách vĩnh viễn.</mark>


- **Khi máy chủ Docker không được đảm bảo có cấu trúc thư mục hoặc tệp nhất định**: Các ổ đĩa giúp bạn tách cấu hình của máy chủ Docker khỏi Container runtime.

- **Khi bạn muốn lưu trữ dữ liệu của vùng chứa trên máy chủ từ xa hoặc nhà cung cấp đám mây, thay vì cục bộ.**

- **Khi bạn cần sao lưu, khôi phục hoặc di chuyển dữ liệu từ máy chủ Docker này sang máy chủ Docker khác**:  

	- Ổ đĩa là lựa chọn tốt hơn. Bạn có thể dừng các Container đang sử dụng ổ đĩa, sau đó sao lưu thư mục của ổ đĩa.


- **Khi ứng dụng của bạn yêu cầu I/O hiệu suất cao trên Docker Desktop**.
	
	- Các ổ đĩa được lưu trữ trong máy ảo Linux chứ không phải trên máy chủ, điều đó có nghĩa là việc đọc và ghi có độ trễ thấp hơn nhiều và thông lượng cao hơn.


- **Khi ứng dụng của bạn yêu cầu hoạt động hệ thống tệp hoàn toàn gốc trên Docker Desktop**
	
	- Ví dụ: một công cụ cơ sở dữ liệu yêu cầu kiểm soát chính xác việc xóa ổ đĩa để đảm bảo độ bền của giao dịch. Các ổ đĩa được lưu trữ trong máy ảo Linux và có thể thực hiện những đảm bảo này, trong khi các bind mounts (liên kết gắn kết) được tách biệt với macOS hoặc Windows, nơi các hệ thống tệp hoạt động hơi khác một chút.


## [Good use cases for bind mounts](https://docs.docker.com/storage/#good-use-cases-for-bind-mounts)

Nói chung, bạn nên sử dụng volume nếu có thể. Bind mount phù hợp với các loại trường hợp sử dụng sau:
mount

- **Chia sẻ tập tin cấu hình từ host đến container**:
	
	- <mark style="background: #FFB86CA6;">Đây là cách Docker cung cấp DNS cho các Container theo mặc định, bằng cách gắn /etc/resolv.conf từ máy chủ vào mỗi container</mark>.

- **Chia sẻ mã nguồn hoặc xây dựng các tạo phẩm giữa môi trường phát triển trên máy chủ Docker và Container.** 
	
	- Ví dụ: bạn có thể gắn thư mục Maven target/ vào một vùng chứa và mỗi lần bạn xây dựng dự án Maven trên máy chủ Docker, Container đó sẽ có quyền truy cập vào các tạo phẩm được xây dựng lại.
	- Nếu bạn sử dụng Docker để phát triển theo cách này, sản phẩm trực tiếp của Dockerfile sẽ sao chép trực tiếp các tạo phẩm sẵn sàng sản xuất vào hình ảnh, thay vì dựa vào liên kết gắn kết.

- **Khi cấu trúc tệp hoặc thư mục của máy chủ Docker được đảm bảo nhất quán với các liên kết gắn kết mà vùng chứa yêu cầu**.
## [Good use cases for tmpfs mounts](https://docs.docker.com/storage/#good-use-cases-for-tmpfs-mounts)


## [Tips for using bind mounts or volumes](https://docs.docker.com/storage/#tips-for-using-bind-mounts-or-volumes)
