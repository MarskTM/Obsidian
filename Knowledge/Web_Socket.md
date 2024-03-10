
Giới thiệu qua về websocket thì đây <mark style="background: #FFB8EBA6;">là một giao thức (protocol full-duplex)</mark> được sử dụng để truyền dữ liệu giữa một máy chủ (server) và một máy khách (client) thông qua một kết nối mạng đơn. <mark style="background: #FFB86CA6;">Nó cho phép ta có thể gửi và nhận dữ liệu một cách liên tục giữa các thành phần mà không cần phải thiết lập lại kết nối theo cơ chế của HTTP1.1</mark>

Các <mark style="background: #D2B3FFA6;">ứng dụng</mark> mà ta có thể dùng WebSocket bao gồm <mark style="background: #BBFABBA6;">các trò chơi trực tuyến, ứng dụng trò chuyện (chat app), cập nhập dữ liệu trong thời gian thực (real-time data updates), ngoài ra còn các ứng dụng website yêu cầu tương tác nhanh chóng</mark>.

# Lập trình Socket với Golang

Hiện tại golang có nhiều gói package hỗ trợ WebSocket dựa trên gói thư viện "net/http" và phổ biết được sử dụng hiện nay là "github.com/gorilla/websocket" (2023). Trong bài viết này tôi sẽ tiến hành cài dặt một số ví dụ mà ta sử dụng WebSocket. 

## Cài đặt một app chat cơ bản 

Trong phần này chúng ta sẽ tiến hành cai đặt một website chat với golang. Đầu tiên chúng ta sẽ định nghĩa 2 đối tượng như sau. 

Ta có cấu trúc thư mục:
```
/backend
	/client.go
	/hub.go
	/main.go
	/home.html
```

### Client model 

Trước tiên ta tiến hành cài đặt đối tượng <mark style="background: #FFB86CA6;">Client.go</mark> như sau:
```
const (
    // Time allowed to write a message to the peer.
    writeWait = 10 * time.Second

    // Time allowed to read the next pong message from the peer.
    pongWait = 60 * time.Second

    // Send pings to peer with this period. Must be less than pongWait.
    pingPeriod = (pongWait * 9) / 10

    // Maximum message size allowed from peer.
    maxMessageSize = 512

)

var (
    newline = []byte{'\n'}
    space   = []byte{' '}
)

  

var upgrader = websocket.Upgrader{
    ReadBufferSize:  1024,
    WriteBufferSize: 1024,
}

  

// Client is a middleman between the websocket connection and the hub.
type Client struct {
    hub *Hub

    // The websocket connection.
    conn *websocket.Conn

    // Buffered channel of outbound messages.
    send chan []byte
}
```

- Khi gửi dữ liệu thông qua WebSocket, <mark style="background: #FFB8EBA6;">người lập trình có thể muốn kết thúc mỗi tin nhắn bằng ký tự dòng mới để</mark> phân biệt giữa các tin nhắn. <mark style="background: #FFB86CA6;">Ký tự dấu cách cũng có thể được sử dụng để phân tách các phần trong dữ liệu nếu cần thiết. </mark>


- **Upgrader** được <mark style="background: #BBFABBA6;">sử dụng để cấu hình và thực hiện việc nâng cấp kết nối HTTP</mark> thông thường thành kết nối WebSocket. 
	- 1. `ReadBufferSize`: Kích thước của bộ đệm đọc (read buffer) được sử dụng khi đọc dữ liệu từ kết nối WebSocket. Trong trường hợp này, kích thước là 1024 byte.
    
	- 2. `WriteBufferSize`: Kích thước của bộ đệm ghi (write buffer) được sử dụng khi ghi dữ liệu vào kết nối WebSocket. Trong trường hợp này, kích thước cũng là 1024 byte.


Tiếp tục ta sẽ khai báo hàm <mark style="background: #FFB86CA6;">serverWs</mark> trong file **client.go**. Hàm này sẽ được đăng kí như một [HTTP handler](HTTP_handler.md) với chương trình chính trong hàm **main**. <mark style="background: #BBFABBA6;">Handler này sẽ giúp ta nâng cấp (upgrades) kết nối HTTP lên giao thức WebSocket</mark>, <mark style="background: #FFB8EBA6;">tạo mới client, sau đó đăng ký client với một kênh tập trung</mark> ([[#Hub model]]) và lên lịch để hủy bỏ đăng ký của client bằng cách sử dụng câu lênh <mark style="background: #FFB86CA6;">defer</mark>.

```
// serveWs handles websocket requests from the peer.
func serveWs(hub *Hub, w http.ResponseWriter, r *http.Request) {
    conn, err := upgrader.Upgrade(w, r, nil)
    if err != nil {
        log.Println(err)
        return
    }

    client := &Client{hub: hub, conn: conn, send: make(chan []byte, 256)}
    client.hub.register <- client

    // Allow collection of memory referenced by the caller by doing all work in
    // new goroutines.
    go client.writePump()
    go client.readPump()

}
```


Cuối cùng là ta sẽ khởi tao 2 hàm chức năng cho đối tượng client trên:

- Đầu tiên [HTTP handler](HTTP_handler.md) sẽ chạy hàm <mark style="background: #FFB86CA6;">writePump()</mark> dưới dạng goroutine. Phượng thức này <mark style="background: #BBFABBA6;">có nhiệm vụ chuyển đổi tin nhắn (messages)</mark> <mark style="background: #D2B3FFA6;">từ kênh gửi tin nhắn của người dùng (client's send channel) tới kết nối WebSocket</mark>.  <mark style="background: #FFB8EBA6;">Phương thức sẽ bị hủy bỏ khi kênh gửi</mark> <mark style="background: #FFB86CA6;">bị đóng bởi kênh tập trung</mark> ([[#Hub model]])

```
// readPump pumps messages from the websocket connection to the hub.
// The application runs readPump in a per-connection goroutine. The application
// ensures that there is at most one reader on a connection by executing all
// reads from this goroutine.
func (c *Client) readPump() {
    defer func() {
        c.hub.unregister <- c
        c.conn.Close()
    }()

    c.conn.SetReadLimit(maxMessageSize)
    c.conn.SetReadDeadline(time.Now().Add(pongWait))
    c.conn.SetPongHandler(func(string) error { c.conn.SetReadDeadline(time.Now().Add(pongWait)); return nil })

    for {
        _, message, err := c.conn.ReadMessage()
        if err != nil {
            if websocket.IsUnexpectedCloseError(err, websocket.CloseGoingAway, websocket.CloseAbnormalClosure) {
                log.Printf("error: %v", err)
            }
            break
        }

        message = bytes.TrimSpace(bytes.Replace(message, newline, space, -1))
        c.hub.broadcast <- message
    }
}

  

// writePump pumps messages from the hub to the websocket connection.
// A goroutine running writePump is started for each connection. The
// application ensures that there is at most one writer to a connection by
// executing all writes from this goroutine.
func (c *Client) writePump() {
    ticker := time.NewTicker(pingPeriod)
    defer func() {
        ticker.Stop()
        c.conn.Close()
    }()
    
    for {
        select {
        case message, ok := <-c.send:
            c.conn.SetWriteDeadline(time.Now().Add(writeWait))
            if !ok {
                // The hub closed the channel.
                c.conn.WriteMessage(websocket.CloseMessage, []byte{})
                return
            }

            w, err := c.conn.NextWriter(websocket.TextMessage)
            if err != nil {
                return
            }

            w.Write(message)
            // Add queued chat messages to the current websocket message.
            n := len(c.send)

            for i := 0; i < n; i++ {
                w.Write(newline)
                w.Write(<-c.send)
            }

            if err := w.Close(); err != nil {
                return
            }
            
        case <-ticker.C:
            c.conn.SetWriteDeadline(time.Now().Add(writeWait))
            if err := c.conn.WriteMessage(websocket.PingMessage, nil); err != nil {
                return
            }
        }
    }
}
```

- Cuối cùng trình xử lý HTTP gọi phương thức <mark style="background: #FFB86CA6;">readPump()</mark> của máy khách. <mark style="background: #ADCCFFA6;">Phương thức này chuyển các tin nhắn gửi đến từ websocket tới hub</mark>.


Kết nối <mark style="background: #ADCCFFA6;">WebSocket hỗ trợ một trình đọc đồng thời và một trình ghi đồng thời</mark>. <mark style="background: #BBFABBA6;">Ứng dụng đảm bảo rằng các yêu cầu đồng thời này được đáp ứng</mark> <mark style="background: #D2B3FFA6;">bằng cách thực thi tất cả các lần đọc</mark> từ <mark style="background: #FFB86CA6;">goroutine <mark style="background: #FFB86CA6;">readPump</mark></mark> và tất cả ghi từ <mark style="background: #FFB86CA6;">goroutine <mark style="background: #FFB86CA6;">writePump</mark></mark>.

Để <mark style="background: #D2B3FFA6;">nâng cao hiệu quả khi chịu tải cao</mark>, hàm <mark style="background: #FFB86CA6;">writePump</mark> sẽ <mark style="background: #FFB8EBA6;">kết hợp các tin nhắn trò chuyện đang chờ xử lý trong kênh gửi thành một tin nhắn WebSocket duy nhất</mark>. Điều này làm giảm số lượng cuộc gọi hệ thống và lượng dữ liệu được gửi qua mạng.
### Hub model




### Lưu ý với số lượng Hub trong hệ thống
Việc xác định số lượng hub cần tạo để phân bổ các phòng chat trong hệ thống chat sử dụng websocket phụ thuộc vào nhiều yếu tố khác nhau như khả năng xử lý của máy chủ, tài nguyên hệ thống, mức độ phân tán của người dùng, và cách tổ chức của ứng dụng.

Tuy nhiên, để đưa ra một ước lượng sơ bộ, bạn có thể sử dụng các phương pháp như sau:

1. **Số lượng người dùng truy cập đồng thời:** Bạn có thể ước lượng số lượng người dùng đồng thời truy cập vào hệ thống chat của bạn. Điều này cung cấp một khung để tính toán số lượng hub cần thiết. Ví dụ, nếu bạn ước lượng có khoảng 10,000 người dùng đồng thời truy cập, đó có thể là một số lượng người dùng đủ lớn để xem xét việc sử dụng nhiều hub.
    
2. **Tài nguyên hệ thống:** Đánh giá tài nguyên hệ thống mà bạn có sẵn để xử lý các kết nối websocket. Mỗi hub sẽ tiêu tốn một lượng tài nguyên nhất định, bao gồm bộ nhớ và khả năng xử lý.
    
3. **Phân tán người dùng:** Nếu người dùng của bạn phân tán trong nhiều phòng chat, bạn có thể cân nhắc sử dụng một số lượng hub tương ứng với số lượng phòng chat. Điều này giúp giảm tải trên mỗi hub và cải thiện hiệu suất.
    
4. **Kiểm tra và điều chỉnh:** Thường xuyên kiểm tra hiệu suất của hệ thống và điều chỉnh số lượng hub khi cần thiết. Một số hệ thống cũng có khả năng tự động mở rộng hoặc thu hẹp số lượng hub dựa trên tải.
    

Với một ước lượng ban đầu, bạn có thể bắt đầu bằng cách tạo một số lượng hub dựa trên các yếu tố trên và sau đó điều chỉnh dần dần khi hệ thống thực sự chạy và bạn có dữ liệu cụ thể hơn về hoạt động của người dùng và tải của máy chủ.



## Ưu và nhược điểm của Web Socket?
| Ưu điểm | Nhược điểm |
| ---- | ---- |
| <mark style="background: #FFB8EBA6;">**Thời gian thực**</mark>: WebSocket cho phép truyền thông hai chiều và thời gian thực giữa máy khách và máy chủ một cách hiệu quả, mà không cần các yêu cầu HTTP mới cho mỗi lượt giao tiếp. Điều này làm cho WebSocket trở thành một lựa chọn tốt cho các ứng dụng cần sự phản hồi nhanh chóng như trò chơi trực tuyến, ứng dụng chat, hoặc cập nhật dữ liệu thời gian thực. | <mark style="background: #FFB86CA6;">**Khó khăn trong việc gỡ lỗi**</mark>: Việc gỡ lỗi trong ứng dụng sử dụng WebSocket có thể khó khăn hơn so với các phương tiện truyền thông khác, do tính không đồng bộ và bản chất của các kết nối WebSocket. |
| **Hiệu suất tốt hơn**: WebSocket tiết kiệm tài nguyên máy chủ và băng thông mạng so với việc sử dụng polling hoặc long-polling trong việc duy trì kết nối hai chiều. | <mark style="background: #FFB86CA6;">**Yêu cầu tài nguyên máy chủ**</mark>: Do WebSocket duy trì kết nối mở lâu dài, điều này có thể tạo áp lực lên tài nguyên máy chủ nếu không được quản lý tốt, đặc biệt là khi có một số lượng lớn các kết nối được duy trì đồng thời. |
| **Giảm độ trễ**: So với các kỹ thuật polling truyền thống, WebSocket giảm thiểu độ trễ và overhead do việc gửi yêu cầu HTTP mới đối với mỗi lượt giao tiếp. | <mark style="background: #FFB86CA6;">**Khả năng tấn công DDoS**</mark>: Do WebSocket cho phép duy trì các kết nối mở lâu dài, nó có thể trở thành mục tiêu của các cuộc tấn công DDoS nếu không được cân nhắc cẩn thận và không được bảo vệ đúng cách. |
|  | **Khả năng đánh cắp dữ liệu**: Nếu không được cài đặt và bảo vệ đúng cách, có nguy cơ dữ liệu được truyền qua WebSocket có thể bị đánh cắp hoặc nghi ngờ về tính toàn vẹn của nó. |


