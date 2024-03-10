

Dockerfile là một tệp văn bản chứa một loạt các hướng dẫn để Docker có thể tự động hóa việc xây dựng một hình ảnh Docker. Dưới đây là <mark style="background: #FFB8EBA6;">một số cách bạn có thể nâng cao</mark> Dockerfile của mình. 

Các cú pháp câu lênh sẽ được viết trong [Dockerfile](Dockerfile_CLI) và [docker-compose.yml](Docker_Compose)

## Sử dụng Multiple Stages

Điều này giúp <mark style="background: #D2B3FFA6;">giảm kích thước của hình ảnh cuối cùng bằng cách sử dụng nhiều tầng. Một tầng để xây dựng ứng dụng và một tầng khác để triển khai ứng dụng.</mark>

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


## Sử dụng Environment Variables

Đặt các biến môi trường trong Dockerfile để <mark style="background: #ADCCFFA6;">tùy chỉnh cấu hình ứng dụng.</mark>

**Ví dụ:**

```none
FROM node:14

ARG NODE_ENV=production
ENV NODE_ENV $NODE_ENV

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

CMD ["npm", "start"]
```


## Sử dụng HEALTHCHECK

Thêm lệnh HEALTHCHECK để <mark style="background: #FFB86CA6;">kiểm tra sức khỏe của ứng dụng trong quá trình chạy</mark>.

**Ví dụ:**

```none
ROM node:14

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

HEALTHCHECK --interval=30s --timeout=10s --start-period=5s \
  CMD node healthcheck.js
```


