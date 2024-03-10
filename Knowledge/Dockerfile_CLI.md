

# Các lệnh chính trong Dockerfile

## FROM

Câu lệnh này chỉ định chúng ta <mark style="background: #FFB8EBA6;">sử dụng image nào để nạp môi trường trong quá trình thực hiện các câu lệnh tiếp theo</mark>. Các images này sẽ được [Docker demon](https://docs.docker.com/get-started/overview/#the-docker-daemon) tải về từ Public Repository hoặc Private Repository riêng của mỗi người tùy theo setup.


**Cú pháp:**

```none
FROM <image> [AS <name>]
FROM <image>[:<tag>] [AS <name>]
FROM <image>[@<digest>] [AS <name>]
```

Câu lệnh này phải được khai báo trên cùng trong file Dockerfile.


## LABEL

Câu lệnh này được dùng để thêm các thông tin meta vào Docker Image khi chúng được build. <mark style="background: #D2B3FFA6;">Có thể chỉ định nhiều label cho một Docker Image, và tất nhiên mỗi cặp key - value phải là duy nhất.</mark> <mark style="background: #BBFABBA6;">Nếu cùng một key mà được khai báo nhiều giá trị (value) thì giá trị được khai báo gần đây nhất sẽ ghi đè lên giá trị trước đó.</mark>

**Cú pháp:**

```none
LABEL <key>=<value> <key>=<value> <key>=<value> ... <key>=<value> 
```

Bạn có thể khai báo metadata cho Image theo từng dòng chỉ thị hoặc có thể tách ra khai báo thành từng dòng riêng biệt.

**Ví dụ:**

```none
LABEL com.example.some-label="lorem"
LABEL version="2.0" description="Lorem ipsum dolor sit amet, consectetur adipiscing elit."
```

Để xem thông tin meta của một Docker Image, ta sử dụng dòng lệnh:

```none
docker inspect <image id>
```

## MAINTAINER

Câu lênh MAINTAINER dùng để khai báo thông tin tác giả người viết file Dockerfile .

**Cú pháp:**

```none
MAINTAINER <name> [<email>]
```

**Ví dụ:**

```none
MAINTAINER NamDH <namduong3699@gmail.com>
```

Hiện nay, theo tài liệu chính thức từ bên phía [Docker](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) thì<mark style="background: #FFB86CA6;"> việc khai báo MAINTAINER đang dần được thay thế bằng LABEL maintainer</mark> bới tính linh hoạt của nó khi ngoài thông tin về tên, email của tác giả thì ta có thể thêm nhiều thông tin tùy chọn khác qua các thẻ metadata và có thể lấy thông tin dễ dàng với câu lệnh `docker inspect ...`.

**Ví dụ:**

```none
LABEL maintainer="namduong3699@gmail.com"
```

## RUN

Chỉ thị **RUN** dùng để <mark style="background: #ADCCFFA6;">chạy một lệnh nào đó trong quá trình build image và thường là các câu lệnh Linux</mark>. Tùy vào image gốc được khai báo trong phần **FROM** thì sẽ có các câu lệnh tương ứng. Ví dụ, để chạy câu lệnh update đối với Ubuntu sẽ là `RUN apt-get update -y` còn đối với CentOS thì sẽ là `Run yum update -y`. <mark style="background: #BBFABBA6;">Kết quả của câu lệnh sẽ được commit lại, kết quả commit đó sẽ được sử dụng trong bước tiếp theo của Dockerfile</mark>.

**Cú pháp:**

```none
RUN <command>
RUN ["executable", "param1", "param2"]
```

**Ví dụ:**

```none
RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'
-------- hoặc --------
RUN ["/bin/bash", "-c", "echo hello"]
```

Ở cách thức `shell form` <mark style="background: #D2B3FFA6;">bạn có thể thực hiện nhiều câu lệnh cùng một lúc với dấu `\`:</mark>

```none
FROM ubuntu
RUN apt-get update
RUN apt-get install curl -y
```

hoặc

```none
FROM ubuntu
RUN apt-get update; \
    apt-get install curl -y
```


## ADD

Chỉ thị **ADD** sẽ<mark style="background: #FFB8EBA6;"> thực hiện sao chép các tập, thư mục từ máy đang build hoặc remote file URLs từ `src` và thêm chúng vào filesystem của image `dest`.</mark>

**Cú pháp:**

```none
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

Trong đó:

- **src** có thể khai báo nhiều file, thư mục, ...
- **dest** phải là đường dẫn tuyệt đối hoặc có quan hệ chỉ thị đối với WORKDIR.

**Ví dụ:**

```none
ADD hom* /mydir/
ADD hom?.txt /mydir/
ADD test.txt relativeDir/
```

<mark style="background: #ADCCFFA6;">Bạn cũng có thể phân quyền vào các file/thư mục mới được copy:
</mark>
```none
ADD --chown=55:mygroup files* /somedir/
ADD --chown=bin files* /somedir/
ADD --chown=1 files* /somedir/
ADD --chown=10:11 files* /somedir/
```


## COPY

Chỉ thị **COPY** cũng giống với **ADD** là copy file, thư mục từ `<src>` và thêm chúng vào `<dest>` của container. Khác với **ADD**, nó<mark style="background: #FFB86CA6;"> không hỗ trợ thêm các file remote file URLs từ các nguồn trên mạng.
</mark>
**Cú pháp:**

```none
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

Ngoài ra chúng ta có thể thực hiện <mark style="background: #D2B3FFA6;">copy các source code thừ trong một image đã được build trước đó vào một images được thực hiện đằng sau</mark> nó bằng cú pháp

**Cú pháp:**

```none
COPY [--from=<name_image>] <src>... <dest>
COPY [--from=<name_image>] ["<src>",... "<dest>"]
```

**Ví dụ:**

```none
# Stage 1: Build stage
FROM node:14 as builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production stage
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```


## ENV

Chỉ thị **ENV** dùng <mark style="background: #FFB86CA6;">để khai báo các biến môi trường</mark>. Các biến này được khai báo dưới dạng _key_ - _value_ bằng các chuỗi. Giá trị của các biến này sẽ có hiện hữu cho các chỉ thị tiếp theo của Dockerfile.

**Cú pháp:**

```none
ENV <key>=<value> ...
```

**Ví dụ:**

```none
ENV DOMAIN="viblo.asia"
ENV PORT=80
ENV USERNAME="namdh" PASSWORD="secret"
```

Ngoài ra cũng có thể thay đổi giá trị của biến môi trường bằng câu lệnh khởi động container:

```none
docker run --env <key>=<value>
```


## CMD

Chỉ thị **CMD** <mark style="background: #BBFABBA6;">định nghĩa các câu lệnh sẽ được chạy sau khi container được khởi động từ image</mark> đã build. Có thể khai báo được nhiều nhưng chỉ có duy nhất **CMD** cuối cùng được chạy.

**Cú pháp:**

```none
CMD ["executable","param1","param2"]
CMD ["param1","param2"] 
CMD command param1 param2
```

**Ví dụ:**

```none
FROM ubuntu
CMD echo Viblo
```



