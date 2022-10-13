# Khái niệm về toolchain

Toolchain là một bộ phần mềm cụ thể đượcc kết nối (chained) với nhau bằng các giai đoạn cụ thể ví dụ như GCCC, binutils, glibc. Ngoài ra, toolchain còn có thể chứa các công cụ khác như debugger hoặc compiler cho một ngôn ngữ cụ thể như C++.

Toolchain cho môi trường embedded linux là một cross toolchain, hay người ta thường hay gọi là cross ccompiler (trình biên dịch chéo). Các chương trình có trong toolchain này đều có thể hoạt động trên máy chủ, nhưng lại tạo ra tệp nhị phân (thực thi) dành cho một kiến trúc vi xử lý khác so với máy chủ.

Đối với embedded linux, việc biên dịch native ngay trên thiết bị vẫn có thể thực hiện được.

## Giới thiệu chung

Khi nói về toolchain, ta sẽ phải phân biệt ba đối tượng máy:

* Máy build: nơi mà toolchain được build ra. Tạm gọi là thành phần A
* Máy host: nơi mà toolchain được sử dụng. (B)
* Máy target: đối tượng mà toolchain hướng tới. (C)

Khi này ta lại có tới bốn loại toolchain với điểm khác biệt nằm ở ba đối tượng trên:

* Toolchain native: chạy trên máy host, biên dịch ra tệp nhị phân có thể hiểu được trên máy host tức A == B == C.
* Cross-compilation toolchain: được biên dịch trên nền máy x86 và dùng để tạo mã nhị phân cho kiến trúc đích (có thể là ARM, MIPS, PowerPC). Khi này: A = B != C.
* Cross-native toolchain: được biên dịch để chạy trên máy target và tạo ra mã nhị phân cho máy đó. A != B == C
* Canadian build: A != B != C

## Thành phần của toolchain

### Binutils

Đây là thành phần đầu tiên của mọi toolchain. Mà cụ thể, GNU Binutils chứa hai công cụ quan trọng là:

* as, assembler, biên dịch mã nhị phân do GCC tạo ra thành mã nhị phân.
* ld, linker, link code object thành một thư viện hoặc tập thực thi.

### Trình biên dịch

Thành phần quan trọng thứ hai chính là trình biên dịch. Đối với môi trường embedded linux thì trình biên dịch phổ biến nhất chính là GCC. GCC không chỉ hỗ trợ C, mà còn là C++, Java, Fortran,... và đồng thời hỗ trợ một dải dài kiến trúc vi xử lý.

### Thư viện C

Đây là thư viện dùng để phát triển phần mềm ứng dụng, giúp tương tác với kernel thông qua các system call.

Hiện có một vài bô thư viện C phổ biến như: glibc, Embedded GLIBC (EGLIBC), uClibc, uClibc-ng, musl.

Thư viện C có mối liên kết đặc biệt với trình biên dịch C, nên việc chọn thư viện cần phải được thực hiện ngay khi toolchain được tạo ra. Lựa chọn này sẽ không thể thay đổi được sau đó.

**Một điểm cần lưu ý khi chọn thư viện C cho toolchain chính là thư viện C được chọn cần phải trùng với thư viện C của thiết bị và có số phiên bản tương đương hoặc thấp hơn.**

Ví dụ Raspebrry Pi 3 mới nhất (13/10/2022) đang sử dụng thư viện glibc, phiên bản 2.31.

[//]: # (TODO: Thêm ảnh chụp kết quả của ldd --version trên Pi.)

### Debugger

Trình gỡ lỗi cũng thường được đi kèm trong các toolchain. Đối với các thiết bị nhúng thì trình biên dịch phổ biến chính là GDB.

## Sysroot

Một thành phần cũng khá quan trọng đối với toolchain đó là sysroot. Đây là thư mục chứa các header, thư viện và các tệp tin cấu hình nhằm phục vụ việc biên dịch chương trình. Thường thì sysroot sẽ được lấy từ trên thiết bị nhúng linux để cung cấp bộ thư viện đầy đủ nhất, đặc biệt là khi biên dịch những phần mềm lớn, phức tạp và đòi hỏi nhiều về dependencies.

Hướng dẫn cách để tạo ra sysroot sẽ có trong phần sau.

## Multiarch

Multiarch là một giải pháp nhằm cài đặt thư viện của một hoặc nhiều kiến trúc khác nhau trên cùng một hệ thống. Khi ấy các thiết bị sử dụng kiến trúc ARM cũng có thể tự cài đặt thư viện của các kiến trúc khác như amd64 hay armel,...

Giải pháp này góp phần vào làm giảm thiểu số lần biên dịch chéo (cross-compile) các thư viện cần dùng. Song chi tiết cụ thể về giải pháp này không nằm trong phạm vi bài viết và sẽ không được giải thích thêm trong tương lai.

Hệ điều hành gốc Debian từ lâu đã sử dụng giải pháp này. Do đó, thay vì thư viện sẽ được lưu toàn bộ chỉ trong thư mục /usr/lib thì nay sẽ được chuyển thành /usr/lib/<arch>. 

Cụ thể, kiến trúc trên Raspberry Pi 3 là arm-linux-gnueabihf nên đường dẫn chi tiết tới thư viện sẽ là: /usr/lib/arm-linux-gnueabihf.

Dù rằng điều này đã được quy ước từ lâu nhưng vẫn cần phải ghi nhớ để áp dụng trong quá trình build toolchain.