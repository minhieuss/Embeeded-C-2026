# SO SÁNH STANDARD C VÀ EMBEDDED C


## 1. Bảng so sánh tổng quan

| Tiêu chí | Standard C | Embedded C |
|---|---|---|
| **Phần cứng**| CPU máy tính, RAM và ROM, điểm chung là dung lượng lớn | MCU (Microcontroller Unit) tích hợp Flash, RAM và ngoại vi, điểm chung là tài nguyên rất khắt khe, chỉ có đến hàng chục KB |
| **Hệ điều hành** | Chạy trên OS (Windows, Linux, macOS) | hạy trên vi điều khiển (MCU) — bare-metal (không có OS) hoặc RTOS  |
| **Trước khi chạy** | OS và runtime chuẩn bị môi trường | User chịu phần lớn trách nhiệm mà OS làm trong C thông thường, cần phải build startup code và linker script (quản lý vùng nhớ) |
| **Chạy chương trình** | OS load từ disk | Flash qua programmer (ST-Link, J-Link...) |
| **Hàm 'main'** | Có thể chạy xong rồi thoát (return 0 ở cuối main) | Thường chạy vòng lặp vô hạn while(1) để liên tục đọc dữ liệu |
| **Đầu vào** | Bàn phím, file, tham số dòng lệnh | Các khối phần cứng: GPIO, ADC, UART, SPI, I²C, CAN… |
| **Đầu ra** | Terminal, file, mạng,... | LED, màn hình, relay, động cơ,...|
| **Bộ nhớ** | Thường có bộ nhớ ảo và bảo vệ theo từng process | MCU nhỏ thường không có bộ nhớ ảo; một số có MPU |
| **Công cụ build** | Compiler cho máy tính | Thường dùng cross compiler cho kiến trúc chip |
| **Kết quả build** | File thực thi như `.exe` | Thường là ELF để debug, HEX/BIN để nạp |
| **Debug** | Debugger sẵn có trên Text Editor | Debug probe, SWD/JTAG, UART và dụng cụ đo |
| **ối ưu** | Tốc độ chạy chương trình  | Kích thước code + RAM + điện năng |
| **Phụ thuộc phần cứng** | Chương trình chạy cách ly và không trực tiếp can thiệp phần cứng | Can thiệp trực tiếp vào thanh ghi phần cứng |


## 2. Các khái niệm và thao tác cốt lõi trong Embedded C

### 2.1. Con trỏ

Hầu hết các vi điều khiển đều ánh xạ thanh ghi ngoại vi vào không gian địa chỉ của CPU, do đó con trỏ có tác dụng truy cập trực tiếp vào địa chỉ thanh ghi và ghi dữ liệu

### 2.2. Thao tác bit

Phần cứng thường dùng từng bit để điều khiển từng chức năng, mỗi chân trên vi điều khiển hay dãy dữ liệu trên thanh ghi đều được mã hóa thành từng bit, do đó thao tác bit giúp ta sửa đúng chức năng cần sửa mà không gán lại dữ liệu của toàn bộ thanh ghi.

Embedded dùng bit thường xuyên vì thanh ghi và giao thức chứa nhiều trường bit. Tuy nhiên, **không áp dụng máy móc các phép đọc-sửa-ghi vào mọi thanh ghi**: có thanh ghi chỉ ghi, đọc có tác dụng phụ, hoặc ghi 1 để xóa cờ.

### 2.3. Từ khóa 'volatile', 'const', 'static'

- 'volatile': các giá trị có thể thay đổi bất ngờ, do phần cứng hoặc ngắt ngoài luồng thực thi hiện tại. Từ khóa trên báo cho trình biên dịch phải thực hiện các lần truy cập cần thiết, không tùy ý dùng lại giá trị cũ.
- 'const': thể hiện dữ liệu không được sửa qua cách truy cập tương ứng. Compiler thường bố trí bảng này vào vùng chỉ đọc
- 'static': giữ giá trị giữa các lần gọi để dùng cho các lần sau.



# LỘ TRÌNH HỌC EMBEDDED C CƠ BẢN

Học theo thứ tự từ **C nền tảng → mạch điện → vi điều khiển → ngoại vi → dự án thực tế**. Có thể học C và mạch điện song song.

### Giai đoạn 1: C nền tảng

- Biến, kiểu dữ liệu, điều kiện, vòng lặp và hàm.
- Mảng, chuỗi, con trỏ, `struct`; `const`, `static`, `volatile`.
- Thao tác bit, chia file `.c`/`.h` và quản lý bộ nhớ cơ bản.

### Giai đoạn 2: Mạch điện cơ bản

- Điện áp, dòng điện, điện trở, công suất; định luật Ohm và Kirchhoff.
- Điện trở, tụ điện, diode, LED, transistor/MOSFET; mạch hạn dòng, chia áp và lọc nguồn.
- Nguồn cấp, GND, tín hiệu analog/digital, mức logic 3,3 V/5 V và điện trở kéo lên/kéo xuống (pull-up/pull-down).
- Đọc sơ đồ mạch, xác định chân linh kiện và dùng đồng hồ vạn năng.

### Giai đoạn 3: Làm quen với vi điều khiển (MCU)

- CPU, Flash, RAM, thanh ghi, clock và reset.
- Đọc datasheet để biết thông số, reference manual để cấu hình ngoại vi, schematic để biết cách nối mạch.
- Tạo project, biên dịch, nạp chương trình và debug trên board.

### Giai đoạn 4: GPIO, timer và ngắt

- GPIO: điều khiển LED, đọc nút nhấn và chống dội phím.
- Timer: đo thời gian, tạo chu kỳ và tránh chờ bằng delay dài.
- Ngắt: xử lý sự kiện phần cứng, giữ hàm phục vụ ngắt ngắn và bảo vệ dữ liệu dùng chung.

### Giai đoạn 5: Giao tiếp UART, I²C và SPI

- UART: gửi log, nhận lệnh từ máy tính.
- I²C/SPI: kết nối cảm biến, màn hình hoặc bộ nhớ ngoài.
- Hiểu cách nối dây, cấu hình giao tiếp, xử lý bộ đệm và timeout.

### Giai đoạn 6: ADC và PWM

- ADC: đọc điện áp analog, chuyển đổi thành giá trị đo và lọc nhiễu cơ bản.
- PWM: điều chỉnh độ sáng LED hoặc điều khiển tải qua mạch công suất phù hợp.
- Kiểm tra giới hạn điện áp, dòng điện và nguồn cấp khi nối MCU với mạch ngoài.

### Giai đoạn 7: Tổ chức firmware và tìm lỗi

- Tách phần điều khiển phần cứng (driver) khỏi logic ứng dụng; dùng máy trạng thái khi cần.
- Quản lý RAM/Flash, stack, buffer và xử lý lỗi.
- Debug bằng breakpoint, log UART và đo tín hiệu thực tế.

### Giai đoạn 8: Dự án thực tế và mở rộng

- Ghép cảm biến, nút nhấn, LED và UART thành một dự án hoàn chỉnh.
- Viết tài liệu nối mạch, cách chạy và kiểm tra các tình huống lỗi.
- Sau khi vững nền tảng, học thêm RTOS: task, lập lịch, queue, semaphore và mutex.
