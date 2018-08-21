
[Source](https://scotch.io/tutorials/the-ins-and-outs-of-token-based-authentication "Permalink to The Ins and Outs of Token Based Authentication ― Scotch")

# The Ins and Outs of Token Based Authentication ― Scotch

## Giới thiệu

Token based authentication nổi lên ở khắp mọi nơi trên web ngày nay. Với hầu hết mọi công ty web sử dụng API, token là cách tốt nhất để xử lý xác thực cho nhiều người dùng.

Có một số yếu tố rất quan trọng khi chọn token based authentication cho ứng dụng của bạn. Lý do chính cho token là:

Máy chủ không có trạng thái và có thể mở rộng 

Sẵn sàng cho ứng dụng mobile

Chuyển xác thực tới các ứng dụng khác

Bảo mật tốt 

## Ai sử dụng Token Based Authentication?

Bất kỳ API hoặc ứng dụng web nào mà bạn đã gặp phải đều có khả năng sử dụng token. Các ứng dụng như Facebook, Twitter, Google+, GitHub và rất nhiều ứng dụng khác sử dụng token.

Chúng ta hãy xem chính xác cách thức hoạt động của nó.

## Why Tokens Came Around

Trước khi chúng ta có thể thấy cách token based authentication hoạt động và lợi ích của nó, chúng ta phải xem xét cách xác thực đã được thực hiện trong quá khứ.

### Xác thực dựa trên máy chủ (Phương thức truyền thống)

> Vì giao thức HTTP là _không có trạng thái_, điều này có nghĩa là nếu chúng ta xác thực người dùng bằng usernane và password, thì trong request tiếp theo, ứng dụng sẽ không biết chúng ta là ai. Chúng ta sẽ phải xác thực lại..

Cách truyền thống để ứng dụng nhớ chúng ta là ai đó là **lưu trữ thông tin người dùng đăng nhập trên máy chủ**. Điều này có thể được thực hiện theo một vài cách khác nhau trong mỗi phiên, thường là trong bộ nhớ hoặc được lưu trữ trên đĩa.

Đây là biểu đồ về quy trình xác thực dựa trên máy chủ sẽ trông như thế nào:

Khi web, ứng dụng và sự nổi lên của ứng dụng di động đã xuất hiện, phương pháp xác thực này đã cho thấy các vấn đề, đặc biệt là trong khả năng mở rộng.

## Các vấn đề của xác thực dựa trên máy chủ

Một số vấn đề lớn nảy sinh với phương pháp xác thực này.

**Sessions**: Mỗi khi người dùng được xác thực, máy chủ sẽ cần tạo bản ghi ở đâu đó trên máy chủ của chúng ta. Điều này thường được thực hiện trong bộ nhớ và khi có nhiều người dùng xác thực, chi phí trên máy chủ của bạn sẽ tăng lên.

**Scalability**: Vì các phiên được lưu trữ trong bộ nhớ, điều này tạo ra các vấn đề với khả năng mở rộng. Khi các nhà cung cấp dịch vụ cloud của chúng ta bắt đầu sao chép các máy chủ để xử lý tải ứng dụng, có thông tin quan trọng trong bộ nhớ phiên sẽ hạn chế khả năng mở rộng của chúng ta.

**CORS**: Khi chúng ta muốn mở rộng ứng dụng của mình để cho phép dữ liệu của chúng ta được sử dụng trên nhiều thiết bị di động, chúng ta phải lo lắng về việc chia sẻ tài nguyên gốc (CORS). Khi sử dụng AJAX để lấy tài nguyên từ một tên miền khác (mobile đến API server), chúng ta có thể gặp sự cố với các request bị cấm

**CSRF**: Chúng ta cũng sẽ có sự bảo vệ chống lại [cross-site request forgery][1] (CSRF). Người dùng dễ bị tấn công CSRF vì họ có thể đã được xác thực với một trang web ngân hàng và điều này có thể được tận dụng khi truy cập các trang web khác.

Với những vấn đề này, khả năng mở rộng là vấn đề chính, bạn nên thử một cách tiếp cận khác.

## Token Based hoạt động như thế nào?

Token based authentication là **không trạng thái**. Chúng ta không lưu trữ bất kỳ thông tin nào về người dùng trên server hoặc trong một session.

Khái niệm này sẽ xử lý được nhiều vấn đề cho việc phải lưu trữ thông tin trên máy chủ.

> Không có thông tin session có nghĩa là ứng dụng của bạn có thể mở rộng quy mô và thêm nhiều máy chủ hơn nếu cần mà không phải lo lắng về nơi người dùng đăng nhập.

Mặc dù việc triển khai này có thể thay đổi, nhưng ý chính của nó như sau:

1. Người dùng truy cập với Username / Password
2. Ứng dụng xác thực thông tin đăng nhập
3. Ứng dụng cung cấp một token đã đăng ký cho client
4. Client lưu trữ token và gửi nó cũng với mỗi request
5. Server xác minh token và trả về dữ liệu

**Mỗi request sẽ yêu cầu token**. Token này sẽ được gửi trong HTTP header để chúng ta tiếp tục với ý tưởng về các request HTTP không trạng thái. Chúng ta cũng sẽ cần thiết lập máy chủ của mình để chấp nhận các request từ tất cả các domain bằng cách sử dụng `Access-Control-Allow-Origin: *`. Điều thú vị về việc chỉ định * trong header ACAO là nó không cho phép request cung cấp thông tin xác thực như xác thực  HTTP, SSL client-side hoặc cookie.

Đây là một hình ảnh minh họa để giải thích quá trình::

Khi chúng ta đã xác thực thông tin của chúng ta và chúng ta có token, chúng ta có thể thử hiện nhiều thứ với token này

Chúng ta thậm chí có thể tạo một permission based token và chuyển nó cùng với ứng dụng của bên thứ ba ( một ứng dụng dành cho thiết bị di động mới mà chúng ta muốn sử dụng) và họ sẽ có thể truy cập dữ liệu của chúng ta - **nhưng chỉ có thông tin chúng ta đã cho phép với token cụ thể đó**.


## Lợi ích của token

### Không có trạng thái và có thể mở rộng

Các token được lưu trữ ở phía client. Hoàn toàn không có trạng thái và sẵn sàng để được mở rộng. Cân bằng tải của chúng ts có thể chuyển người dùng đến bất kỳ máy chủ nào của chúng ta khi không có thông tin trạng thái hoặc phiên nào.

Nếu chúng ta giữ thông tin session của người dùng đã đăng nhập, điều này sẽ yêu cầu chúng ta tiếp tục gửi người dùng đó đến máy chủ họ đã đăng nhập (được gọi là tính liên thông session).

Điều này mang lại vấn đề khi một số người dùng sẽ buộc phải đến cùng một máy chủ và điều này có thể dẫn đến một điểm có lưu lượng truy cập  lớn.

Đừng lo lắng ! Những vấn đề này đã biến mất với token vì token đã giữ dữ liệu cho người dùng đó.

### Bảo mật

Token không phải cookie, được gửi theo mọi request và vì không có cookie nào được gửi, điều này giúp ngăn chặn các cuộc tấn công CSRF. Ngay cả khi việc triển khai cụ thể của bạn lưu trữ token trong một cookie ở phía client thì cookie cũng chỉ là một cơ chế lưu trữ thay vì một cơ chế xác thực. Không có thông tin nào dựa trên session để thao tác vì chúng ta không có session!

Token cũng hết hạn sau một khoảng thời gian nhất định, do đó, người dùng sẽ được yêu cầu đăng nhập lại một lần nữa. Điều này giúp chúng ta luôn an toàn. Ngoài ra còn có khái niệm về [thu hồi token][2] cho phép chúng tôi vô hiệu hóa token cụ thể và thậm chí là một nhóm token dựa trên cùng một sự cho phép.

### Khả năng mở rộng (Bạn của bạn và các quyền)

Tokens sẽ cho phép chúng ta xây dựng các ứng dụng chia sẻ quyền với nhau. Ví dụ, chúng ta đã liên kết các tài khoản xã hội ngẫu nhiên với những tài khoản chính của chúng ta như Facebook hoặc Twitter.

Khi chúng ta đăng nhập Twitter thông qua một dịch vụ (như Buffer), chúng ta đang cho phép Buffer đăng lên Twitter stream của chúng ta.

Bằng cách sử dụng token, đây là cách chúng ta **cung cấp quyền chọn lọc cho các ứng dụng của bên thứ ba**. Chúng ta thậm chí có thể xây dựng API của riêng mình và cung cấp các token quyền đặc biệt nếu người dùng của chúng ta muốn cấp quyền truy cập vào dữ liệu của họ cho một ứng dụng khác.

### Nhiều nền tảng và domain

Chúng ta đã nói một chút về CORS trước đó. Khi ứng dụng và dịch vụ của chúng ta mở rộng, chúng ta sẽ cần cung cấp quyền truy cập vào tất cả các loại thiết bị và ứng dụng (vì ứng dụng của chúng ta chắc chắn sẽ trở nên phổ biến!).

Khi API của chúng ta chỉ phân phối dữ liệu, chúng ta cũng có thể đưa ra lựa chọn thiết kế để phân phát nội dung từ CDN. Điều này giúp loại bỏ các vấn đề mà CORS mang lại sau khi chúng ta thiết lập cấu hình header nhanh cho ứng dụng của chúng ta.
    
    
    Access-Control-Allow-Origin: *
    

Dữ liệu và tài nguyên của chúng tôi sẵn có cho các yêu cầu từ bất kỳ miền nào ngay bây giờ **miễn là người dùng có token hợp lệ**.

### Dựa trên các tiêu chuẩn

Khi tạo token, bạn có một vài tùy chọn. Chúng ta sẽ đi sâu hơn vào chủ đề này khi chúng ta bảo mật một API trong một bài viết tiếp theo, nhưng tiêu chuẩn để sử dụng sẽ là [JSON Web Tokens][3].

Biểu đồ thư viện và trình debug tiện dụng này hiển thị hỗ trợ cho các JSON Web Tokens. Bạn có thể thấy rằng nó có nhiều hỗ trợ trên nhiều ngôn ngữ. Điều này có nghĩa là bạn có thể sẵn sàng chuyển đổi cơ chế xác thực của mình nếu bạn muốn làm như vậy trong tương lai!

## Kết luận

Đây chỉ là xem xét cách thức và lý do của token based authentication. Như mọi khi trong thế giới an ninh, có nhiều, nhiều, nhiều, nhiều (quá nhiều?) Nhiều hơn cho mỗi chủ đề và nó thay đổi theo từng trường hợp sử dụng. Thậm chí, chúng ôi cũng nghiên cứu một số chủ đề về khả năng mở rộng để phù với cuộc trò chuyện của riêng họ.

Đây là một bài tổng quan nhanh, vì vậy xin vui lòng chỉ ra bất cứ điều gì đã bị bỏ lỡ hoặc bất kỳ câu hỏi nào bạn có về vấn đề này.


  
