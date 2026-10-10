# SO SÁNH STANDARD C VÀ EMBEDDED C


## 1. Bảng so sánh tổng quan

| Tiêu chí | Standard C | Embedded C |
|---|---|---|
| Phần cứng | CPU máy tính, RAM và ROM, điểm chung là dung lượng lớn | MCU (Microcontroller Unit) tích hợp Flash, RAM và ngoại vi, điểm chung là tài nguyên rất khắt khe, chỉ có đến hàng chục KB |
| Hệ điều hành | Chạy trên OS (Windows, Linux, macOS) | hạy trên vi điều khiển (MCU) — bare-metal (không có OS) hoặc RTOS  |
| Trước khi chạy | OS và runtime chuẩn bị môi trường | User chịu phần lớn trách nhiệm mà OS làm trong C thông thường, cần phải build startup code và linker script (quản lý vùng nhớ) |
| Chạy chương trình | OS load từ disk | Flash qua programmer (ST-Link, J-Link...) |
| Hàm 'main' | Có thể chạy xong rồi thoát (return 0 ở cuối main) | Thường chạy vòng lặp vô hạn while(1) để liên tục đọc dữ liệu |
| Đầu vào | Bàn phím, file, tham số dòng lệnh | Các khối phần cứng: GPIO, ADC, UART, SPI, I²C, CAN… |
| Đầu ra | Terminal, file, mạng,... | LED, màn hình, relay, động cơ,...|
| Bộ nhớ | Thường có bộ nhớ ảo và bảo vệ theo từng process | MCU nhỏ thường không có bộ nhớ ảo; một số có MPU |
| Công cụ build | Compiler cho máy tính | Thường dùng cross compiler cho kiến trúc chip |
| Kết quả build | File thực thi như `.exe` | Thường là ELF để debug, HEX/BIN để nạp |
| Debug | Debugger sẵn có trên Text Editor | Debug probe, SWD/JTAG, UART và dụng cụ đo |
| Tối ưu | Tốc độ chạy chương trình  | Kích thước code + RAM + điện năng |
| Phụ thuộc phần cứng | Chương trình chạy cách ly và không trực tiếp can thiệp phần cứng | Can thiệp trực tiếp vào thanh ghi phần cứng |


## 2. Các khái niệm và thao tác cốt lõi trong Embedded C

### 2.1. Con trỏ

Hầu hết các vi điều khiển đều ánh xạ thanh ghi ngoại vi vào không gian địa chỉ của CPU, do đó con trỏ có tác dụng truy cập trực tiếp vào địa chỉ thanh ghi và ghi dữ liệu

### 2.2. Thao tác bit

Phần cứng thường dùng từng bit để điều khiển từng chức năng, mỗi chân trên vi điều khiển hay dãy dữ liệu trên thanh ghi đều được mã hóa thành từng bit, do đó thao tác bit giúp ta sửa đúng chức năng cần sửa mà không gán lại dữ liệu của toàn bộ thanh ghi.

Embedded dùng bit thường xuyên vì thanh ghi và giao thức chứa nhiều trường bit. Tuy nhiên, **không áp dụng máy móc các phép đọc-sửa-ghi vào mọi thanh ghi**: có thanh ghi chỉ ghi, đọc có tác dụng phụ, hoặc ghi 1 để xóa cờ. Phải xem reference manual.

### 2.3. Từ khóa 'volatile', 'const', 'static'

- 'volatile': các giá trị có thể thay đổi bất ngờ, do phần cứng hoặc ngắt ngoài luồng thực thi hiện tại. Từ khóa trên báo cho trình biên dịch phải thực hiện các lần truy cập cần thiết, không tùy ý dùng lại giá trị cũ.
- 'const': thể hiện dữ liệu không được sửa qua cách truy cập tương ứng. Compiler thường bố trí bảng này vào vùng chỉ đọc
- 'static': giữ giá trị giữa các lần gọi để dùng cho các lần sau.


### 2.4 Các ngoại vi thường học

| Ngoại vi | Mục đích thường gặp | Ví dụ |
|---|---|---|
| GPIO | Đọc/ghi mức logic | Nút nhấn, LED |
| ADC | Đổi tín hiệu analog thành số | Đo điện áp, đọc cảm biến analog |
| Timer/PWM | Đếm thời gian, tạo xung | Điều chỉnh độ sáng LED |
| UART | Giao tiếp nối tiếp | Log với máy tính |
| SPI | Giao tiếp đồng bộ | Màn hình, chip nhớ |
| I²C | Giao tiếp bus có địa chỉ | Cảm biến, EEPROM |
| CAN | Giao tiếp theo bus | Hệ thống điều khiển có các node CAN |

Trong embedded, ngoài định dạng dữ liệu còn phải cấu hình chân, tốc độ, clock, timeout và kiểm tra tín hiệu điện.


# LỘ TRÌNH HỌC EMBEDDED C CƠ BẢN

### Giai đoạn 1: C nền tảng


- Kiểu số, dấu, phép chuyển kiểu và giới hạn biểu diễn.
- Hàm, tham số, giá trị trả về và phạm vi biến.
- Mảng, chuỗi, con trỏ,.
- Khác biệt giữa một mảng và một con trỏ trỏ vào mảng.
- `struct`, `enum`, `typedef`, `static`, `const`.
- Phép AND/OR/XOR, dịch bit và mask.
- Chia file `.c`/`.h`, khai báo và định nghĩa.
- Hành vi không xác định: vượt biên, con trỏ không hợp lệ, tràn số nguyên có dấu.

> [!NOTE]
> Không cần học thuật toán phức tạp trước khi làm GPIO, nhưng phải vững cách quản lý dữ liệu và bộ nhớ

### Giai đoạn 2: Hiểu MCU và chạy chương trình đầu tiên

- CPU, Flash, RAM, GPIO và ngoại vi là gì.
- Clock, reset và lý do firmware cần startup code.
- Thanh ghi và địa chỉ bộ nhớ ở mức khái niệm.
- Quá trình compile → link → nạp → chạy.
- Vai trò của file ELF, HEX/BIN và linker script.
- Datasheet, reference manual và schematic dùng để trả lời câu hỏi nào.

### Giai đoạn 3: GPIO và nút nhấn

- Input/output, pull-up/pull-down và mức kích hoạt.
- Cách bật clock ngoại vi và cấu hình chân theo SDK.
- LED active-high/active-low.
- Dội nút nhấn và debounce bằng thời gian.

### Giai đoạn 4: UART và parser lệnh

- TX/RX, baud rate, data bits, parity và stop bits.
- Sự khác nhau giữa byte nhận và một lệnh hoàn chỉnh.
- Buffer nhận, ký tự kết thúc lệnh và giới hạn chiều dài.
- Trạng thái nhận, timeout và xử lý dữ liệu sai.

### Giai đoạn 5: Timer và chương trình không chặn lâu

- Tick, timer phần cứng, prescaler và chu kỳ đếm.
- Chờ blocking so với kiểm tra thời điểm đến hạn.
- So sánh khoảng thời gian bằng phép trừ unsigned, cùng giả thiết về độ rộng bộ đếm và tần suất kiểm tra.
- Khác biệt giữa chạy nhanh và đáp ứng deadline.

### Giai đoạn 6: Ngắt và dữ liệu dùng chung

- ISR, ưu tiên ngắt và thời gian phục vụ ngắt.
- Dữ liệu chia sẻ giữa ISR và luồng chính.
- `volatile` và giới hạn của nó.
- Tính nguyên tử, thao tác đọc-sửa-ghi và critical section.
- Cờ sự kiện, bộ đếm và ring buffer.

### Giai đoạn 7: ADC và PWM

- Độ phân giải ADC, điện áp tham chiếu và thời gian lấy mẫu.
- Chuyển giá trị ADC thành đại lượng cần đo theo cấu hình thực tế.
- Tần số PWM và duty cycle.
- Lọc trung bình đơn giản và đánh đổi độ trễ.

### Giai đoạn 8: I²C, SPI và đọc datasheet

- I²C: địa chỉ, ACK/NACK, pull-up và giao dịch đọc/ghi.
- Phân biệt địa chỉ I²C 7 bit với cách API của SDK biểu diễn địa chỉ.
- SPI: clock, MOSI/MISO, chip select, CPOL/CPHA và thứ tự bit.
- Thanh ghi thiết bị, ID, hệ số chuyển đổi và trạng thái dữ liệu sẵn sàng.
- Timeout và cách báo lỗi khi giao tiếp thất bại.

### Giai đoạn 9: Tổ chức firmware, bộ nhớ và kiểm thử

- Tách logic, driver và cấu hình board.
- State machine và chuyển trạng thái theo sự kiện.
- Mã lỗi, timeout, retry có giới hạn và phục hồi lỗi.
- Flash, `.data`, `.bss`, stack, heap và map file.
- Giới hạn buffer, vòng đời con trỏ và chi phí cấp phát.
- Đọc call stack, thanh ghi và nguyên nhân reset/lỗi CPU theo chip.

### Giai đoạn 10: RTOS cơ bản

- Task, trạng thái Ready/Running/Blocked và mức ưu tiên.
- Delay nhường CPU, lập lịch định kỳ và thời gian xử lý.
- Queue, semaphore, mutex và khác biệt mục đích sử dụng.
- Priority inversion ở mức khái niệm.
- Stack riêng cho task và các kiểm tra mà RTOS hỗ trợ.
- API được phép dùng trong ISR và giới hạn ưu tiên ngắt theo port.

### Giai đoạn 11: Tích hợp và hoàn thiện dự án