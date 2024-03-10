
Đa số các ngôn ngữ lập trình hiện nay đều hỗ trợ [lập trình hướng đối tượng](https://viblo.asia/p/undefined-07LKXA7kZV4) ở nhiều mức độ khác nhau. Và Go cũng hỗ trợ lập trình hướng đối tượng theo cách riêng của [Go](https://viblo.asia/p/undefined-07LKXA7kZV4).

# 1. Các thuật ngữ

> [_“Go has types and values rather than classes and objects.”_](https://nathany.com/good/)

<mark style="background: #FFB86CA6;">Điều quan trọng đầu tiên là bạn phải hiểu rằng có thể lập trình hướng đối tượng trong Go mà không cần phải có đối tượng.</mark> Thuật ngữ "Object" sẽ rất khác khi bạn làm việc với Go.

Trong Go, chúng ta có các **giá trị** (values), trong khi các ngôn ngữ OOP truyền thống có các **đối tượng** (objects), hãy phân biệt hai khái niệm này.

## Object với Value

Một **đối tượng** là thể hiện của một lớp. Đối tượng được truy vấn thông qua một tham chiếu có tên.

```php
<?php
class SomeObject {
    public $classVar;
    public function __construct( $classVar ) {
        $this->classVar = $classVar;
    }
}

    
$object    = new SomeObject( "Hello, world." );
$reference = $object;
$reference->classVar = "Look! I can access object!";
echo $object->classVar;    
echo $reference->classVar; 
```

Mã PHP này minh họa rằng cả **object** và **reference** trỏ đến cùng một instantiation của **SomeObject**.

Ngoài việc có thể được truy cập bởi các tham chiếu có tên khác nhau, các đối tượng cũng bao gồm các khái niệm như kế thừa và các lớp con, điều này không thể thực hiện được với Go. Vì vậy, khi học Go, tốt nhất là không quan tâm đến các ràng buộc với đối tượng, và tập trung vào việc sử dụng các thuật ngữ chính xác.

```go
package main
import ("fmt")
type SomeStruct struct{
    Field string
}
func main() {
    value := SomeStruct{Field: "Structs are values"}
    copy  := value
    copy.Field = "This is a Copy & doesn't change the variable value"
    fmt.Println(value.Field)
    fmt.Println(copy.Field)
}

```

Trong ví dụ trên, bạn có thể thấy chúng ta gán **copy.Field, copy.Field** không bao giờ thay đổi các giá trị của nó. Khi chúng ta muốn tham chiếu cùng một trường hợp, <mark style="background: #FFB86CA6;">giống như C </mark>chúng ta có con trỏ để thực hiện điều đó.

## Types & Method Set

Bây giờ các bạn sẽ biết tại sao Go không có các đối tượng, cùng tìm hiểu cách hoạt động trên một instance của một **Type**, cụ thể là một kiểu cấu trúc.

```go
type SomeStruct struct{
    Field string
}
// foo nằm trong tập các method của SomeStruct
// (s *SomeStruct) là một receiver mà con trỏ SomeStruct trỏ đến
func (s *SomeStruct) foo(field string) {    
    s.Field = field
}
func main() {
    someStruct := new(SomeStruct)
    
    someStruct.foo("a")
    fmt.Println(someStruct.Field)  // "a"
    someStruct.foo("b")
    fmt.Println(someStruct.Field)  // "b"
}
```

Ở đây, chúng ta thấy rằng phương thức **foo** hoạt động trên cùng một instance của **someStruct** và thay đổi giá trị **Field** của nó.

<mark style="background: #BBFABBA6;">Nhắc lại, Go không có các đối tượng, nhưng chúng ta có thể thấy sự tương đồng giữa các phương thức nhận và các phương thức lớp.</mark>

---

Bây giờ chúng ta có thể tìm hiểu các OOP patterns trong Go cụ thể như thế nào


# Đóng gói (Encapsulation)

> [“Encapsulation is the mechanism of hiding of data implementation by restricting access to public methods.”](https://crackingjavainterviews.blogspot.com/2013/04/what-are-four-principles-of-oop.html)

Trong hầu hết các ngôn ngữ lập trình phổ biến, <mark style="background: #D2B3FFA6;">việc đóng gói trong OOP dựa trên lớp đạt được thông qua private và public class variables / methods. Trong Go, đóng gói được thực hiện trên các <mark style="background: #FFB86CA6;">package</mark> level.</mark>

<mark style="background: #BBFABBA6;">Các thành phần "public" có thể được xuất ra bên ngoài các package và được trình bày bằng cách viết hoa chữ cái đầu tiên</mark>. Ở đây, publlic được đặt trong dấu ngoặc kép bởi vì <mark style="background: #ADCCFFA6;">thuật ngữ chính xác hơn là exported</mark> và unexported elements, tuy nhiên dùng từ public sẽ giúp các bạn nắm bắt nhanh hơn. Unexported elements được chỉ định bằng chữ cái đầu tiên và chỉ có thể truy cập được trong package tương ứng.

Trong package _<mark style="background: #FFB86CA6;">encapsulation</mark>_, Encapsulation (struct), Expose (method), và Unhide (method) <mark style="background: #FFB8EBA6;">tất cả đều có thể được sử dụng từ các packages khác (khái niệm go module)</mark>. 

```go
import "github.com/amy/tech-talk/encapsulation"
func main() {
    e := encapsulation.Encapsulation{}    
    e.Expose()    
    // e.hide()    //nếu bỏ comment,  xuất hiện lỗi
                   // ./main.go:10: e.hide undefined (cannot refer
                   // to unexported field or method encapsulation.
                   // (*Encapsulation)."".hide)
    
    e.Unhide()
}
```

# Đa hình (Polymorphism)

> [“Polymorphism describes a pattern in object oriented programming in which classes have different functionality while sharing a common interface.”](https://stackoverflow.com/questions/1031273/what-is-polymorphism-what-is-it-for-and-how-is-it-used)

Như chúng ta thường thấy ở các ngôn ngữ lập trình hướng đối tượng,<mark style="background: #FFB86CA6;"> tính đa hình thể hiện khi các lớp kế thừa cùng một lớp</mark>. <mark style="background: #FFB8EBA6;"><mark style="background: #BBFABBA6;">Với việc sử dụng interface</mark>, mặc dù không có khái niệm kế thừa nhưng Go cũng hỗ trợ tính đa hình theo cách riêng của nó.</mark>

```go
package polymorphism 
import "fmt"
type SloganSayer interface {
    Slogan()
}
// SaySlogan truyền vào một tham số kiểu SloganSayer
func SaySlogan(sloganSayer SloganSayer) {
    sloganSayer.Slogan()
}


// Hillary thỏa mãn SloganSayer interface
// bằng việc thực thi function Slogan.
// Vì vậy, Hillary cũng là một kiểu của SloganSayer.
type Hillary struct{}
func (h *Hillary) Slogan() {
    fmt.Println("Stronger together.")
}
// tương tự với struct Trump
type Trump struct{}
func (t *Trump) Slogan() {
    fmt.Println("Make America great again.")
}
```

```go
package main 
import "github.com/amy/tech-talk/polymorphism"
func main() {
    hillary := new(polymorphism.Hillary)
    hillary.Slogan()                  // "Stronger together."
    polymorphism.SaySlogan(hillary)   // "Stronger together."
    trump := new(polymorphism.Trump)
    polymorphism.SaySlogan(trump)     // "Make America great again."
}
```

<mark style="background: #FFB86CA6;">Trong ví dụ trên, ta không cần quan tâm ứng cử viên nào đang nói khẩu hiệu. Miễn là một kiểu implements của SloganSayer interface, chúng ta có thể truyền nó vào SaySlogan.</mark>

### Vì sao phải dùng Tính đa hình?

Nhìn chung, nếu lập trình viên tận dụng được Tính đa hình thì sẽ mang lại nhiều lợi ích trong quá trình phát triển phần mềm. Những lợi ích đó có thể là:

- Lập trình viên không phải viết lại mã hoặc lớp đã có sẵn. Sau khi một đoạn mã hoặc lớp được khởi tạo thành công, ta có thể tái sử dụng chúng nhờ vào Tính đa hình.
- Lập tình viên có thể dùng một tên duy nhất để lưu trữ biến của nhiều kiểu dữ liệu khác nhau (float, double, long, int,…).
- Lập trình viên có thể phát triển thêm Tính trừu tượng từ những đoạn mã đơn giản. Bạn có thể tham khảo nội dung này ở những bài viết liên quan.
# Composition (có thể hiểu như inheritance)

Trong **Go**, thừa kế là không tồn tại. Thay vào đó,<mark style="background: #FFB8EBA6;"> chúng ta xây dựng các cấu trúc với các yếu tố tổng hợp và tái sử dựng thông qua <mark style="background: #D2B3FFA6;">embedding</mark> (nhúng).
</mark>

Go cho phép chúng ta embed các loại bên trong interface hoặc structs. Thông qua embedding, chúng ta có thể biến các phương thức được included từ bên trong hay bên ngoài.

> [When we embed a type, the methods of that type become methods of the outer type, but when they are invoked the receiver of the method is the inner type, not the outer one.](https://golang.org/doc/effective_go.html#embedding)

```go
package composition 
import "fmt"
type Human struct {
    FirstName   string
    LastName    string
    CanSwim     bool
}
// Amy được embedded với kiểu Human
// và do đó Amy có thể gọi các phương thức của Human
type Amy struct {
    Human
}
// Alan được embedded với kiểu Human 
type Alan struct {
    Human
}
func (h *Human) Name() {
    
    fmt.Printf("Hello! My name is %v %v", h.FirstName, h.LastName)
}
func (h *Human) Swim() {
    
    if h.CanSwim == true {
        fmt.Println("I can swim!")
    } else {
        fmt.Println("I can not swim.")
    }
}
```

# Trừu tượng (Abstraction)

> [“Abstraction means working with something we know how to use without knowing how it works internally.”](http://www.introprogramming.info/english-intro-csharp-book/read-online/chapter-20-object-oriented-programming-principles/#_Toc362296567)

Tương tự như embedding các structs bên trong một struct, chúng ta cũng có thể <mark style="background: #FFB86CA6;">embed các interfaces trong các structs. Bất kỳ kiểu nào thỏa mãn interface nào cũng sẽ sử dụng được interface đó.</mark>

```go
package abstraction
import "fmt"
type SloganSayer interface {
    Slogan()
}
// Campaign có thể accept a SloganSayer trong quá trình khởi tạo
// Campaign cũng là một SloganSayer bởi vì nó cũng implements SloganSayer interface.
type Campaign struct{
    SloganSayer
}
// SaySlogan cũng có thể accept Campaign như là một tham số truyền vào!
func SaySlogan(s SloganSayer) {
    s.Slogan()
}
// Hillary implements the SloganSayer interface
// Hillary là một SloganSayer
type Hillary struct{}
func (h *Hillary) Slogan() {
    fmt.Println("Stronger together.")
}
// Tương tự với Trump 
type Trump struct{}
func (t *Trump) Slogan() {
    fmt.Println("Make American great again.")
}
```


```go
package main
import "github.com/amy/tech-talk/abstraction"
func main() {
    hillary := new(abstraction.Hillary)
    trump := new(abstraction.Trump)
    h := abstraction.Campaign{hillary}
    t := abstraction.Campaign{trump}
    // Triển khai slogan tranh cử của Trump và hilary được trừu tượng hóa đi.
    // Thay vào đó. Campaign chỉ biết rằng có đó là một SloganSayer
    // và do đó có thể gọi Slogan.
    h.Slogan()  // "Stronger together."
    t.Slogan()  // "Make America great again."
    // Chúng ta có thể inject một  SloganSayer vào tham số SaySlogan
    abstraction.SaySlogan(hillary)  // "Stronger together."
    abstraction.SaySlogan(trump)    // "Make America great again."
    // h và t cũng là một loại campaign
    abstraction.SaySlogan(h)  // "Stronger together."
    abstraction.SaySlogan(t)  // "Make America great again."
}
```