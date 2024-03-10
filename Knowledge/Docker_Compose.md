

- Docker-compose là 1 <mark style="background: #ADCCFFA6;">công cụ để định nghĩa và chạy nhiều container trên ứng dụng Docker</mark>.
- Cho phép bạn <mark style="background: #BBFABBA6;">tạo 1 file cấu hình YML,</mark> nơi mà bạn sẽ định nghĩa các services trên ứng dụng của bạn và <mark style="background: #BBFABBA6;">định nghĩa tất cả các bước, cấu hình cần thiết để xây dựng các image, up các container và liên kết chúng với nhau</mark>. Cuối cùng, 1 khi tất cả điều này được thực hiện hì bạn sẽ chỉ cần thiết lập tất cả với 1 câu lệnh duy nhất.
- <mark style="background: #D2B3FFA6;">Ưu điểm: giúp tự động tải các image, thiết lập cấu hình</mark> tốt hơn rất nhiều so với [Docker](Docker_Overview).


# Run command

-  Build các images (nếu cần tạo)
```none
 $ docker-compose build
```

- Chạy container và build image
```none
$ docker-compose up --build -d
```

- Khởi chạy các services bên trong background
```none
 $ docker-compose up -d
```

## Stop & Remove container, network, image và volume

```none
 $ docker-compose down
```

Ví dụ: `docker-compose down -v`.

<mark style="background: #FFB86CA6;">Một số tùy chọn hữu ích</mark>:
- `--rmi type`: Xóa image.
- `-v, --volumes`: Xóa các volumes được đặt tên được khai báo trong phần `volume` của file compose và các volumes ẩn danh được đính kèm vào các `container`.
- `--remove-orphans`: Xóa các `container` cho các service không được xác định trong tệp `compose`


## Remove container

**Liệt kê tất cả các container**:
```none
 $ docker  ps -a
```

**Xóa container đang chạy**:
```none
 $ docker rm <container_name> -f
```

## Remove images:

**Liệt kế tất cả các image:**
```none
 $ docker images
```

**Xóa image đang chạy:**
```none
 $ docker rmi <image_id> -f
```

> Note: `'-f'`: dùng để buộc xóa, ngay cả khi `container` đang chạy hoặc image đang được sử dụng.





# **Giải thích cú pháp của file docker-compose.yml**


#### File docker-compose.yml được tổ chức gồm 4 phần:
| Comand | Ý nghĩa |
| ---- | :--- |
| version | Version của file `docker-compose` |
| services | Chứa các container. Với <mark style="background: #D2B3FFA6;">mỗi service là tên của một container</mark> |
| [volumes](Docker_Engine.md) | Gắn đường dẫn trên `host machine` được sử dụng trên `container` |
| networks | Sử dụng để cấu hình `network` cho ứng dụng |
#### Với services có một số thành phần sau:
|  |  |
| ---- | ---- |
| image | Chỉ ra image sẽ được dùng để build container. Tên image được chỉ định khi build một image trên máy host hoặc download từ Docker Hub |
| port | Kết nối port của <mark style="background: #ADCCFFA6;">máy host</mark> đến port của <mark style="background: #ADCCFFA6;">container</mark> |
| [volumes](Docker_Engine.md) | Gắn đường dẫn trên `host machine` được sử dụng trên `container` |
| enviroment | Định nghĩa các biến môi trường được truyền vào Docker |
| depends_on | Chọn các `service` được dùng là `dependency` cho `container` được xác định trong `service` hiện tại. |
| build | Chỉ ra vị trị đường dẫn đặt `Dockerfile` bằng thuộc tính `contex: <path>` |


**Ví dụ:**

```none
version: '3.3'

services:

  builderpro:

    image: intern-frontend-builder:builder

    build:

      context: . # Use an image built from the specified dockerfile in the current directory.

      dockerfile: Dockerfile.builder

    networks:

      - frontend

  

  webpro:

    image: intern-frontend:frontend

    build:

      context: . # Use an image built from the specified dockerfile in the current directory.

      dockerfile: Dockerfile

    ports:

      - '3000:80'

    networks:

      - frontend

networks:

  frontend:

    driver: bridge
```

