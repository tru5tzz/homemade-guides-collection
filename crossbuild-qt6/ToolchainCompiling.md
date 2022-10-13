# Cách build toolchain phù hợp

Để build toolchain ta có nhiều cách. [Trong bài viết này](https://preshing.com/20141119/how-to-build-a-gcc-cross-compiler/), tác giả build hoàn toàn thủ công. Song từng thành phần trong mỗi toolchain đều là các chương trình có kích thước lớn và phức tạp nên quá trình build thủ công có thể tốn rất nhiều công sức trong việc thiết lập môi trường, dependencies,...

Do đó để đơn giản hóa quá trình build toolchain thì chúng ta sẽ dùng crosstool-ng. Đây là bản fork lại của crosstool và đã được phát triển cẩn thận hướng tới mục đơn giản hóa quá trình thiết lập và sử dụng.

## Cài đặt crosstool-ng

[Documentation đầy đủ của crosstool-ng có thể xem tại đây.](https://crosstool-ng.github.io/docs/)

[Tải về crosstool-ng thông qua tarball hoặc clone source.](https://crosstool-ng.github.io/download/) Ở hiện tại, phiên bản mới nhất là 1.25.0.

Sau khi đã clone được git repo của công cụ này về, ta tiến hành build và cài đặt.

### Cài đặt môi trường

#### Linux

Cài đặt dependencies:

```bash
    sudo apt update
    sudo apt install -y gcc g++ gperf bison flex texinfo help2man make libncurses5-dev \
    python3-dev autoconf automake libtool libtool-bin gawk wget bzip2 xz-utils unzip \
    patch libstdc++6 rsync git meson ninja-build build-essential
```

#### Windows

### Build crosstool-ng

Sau khi đã clone git repo của crosstool-ng về, ta thực hiện câu lệnh dưới để đưa repo về phiên bản 1.25.0:

```bash
    cd crosstool-ng
    git checkout crosstool-ng-1.25.0
```

Sau đó là chạy đoạn script ```bootstrap``` trước khi build

```bash
    ./bootstrap
```

Có một vài cách thiết lập crosstool-ng, nhưng thuận tiện nhất sẽ là dùng thư mục này làm nơi cài đặt luôn. Sau khi configure xong thì có thể chạy make để build crosstool-ng.

```bash
    ./configure --enable-local
    make
```

Việc cài đặt tới đây đã là xong. Tiếp theo sẽ là bước thiết lập và build toolchain.

## Thiết lập toolchain

### Crosstool-ng menuconfig

Việc thiết lập chi tiết toolchain sẽ được thực hiện thông qua hai cách: sử dụng câu lệnh ```./ct-ng menuconfig``` để thao tác trong giao diện trực quan, hai là dùng bất kì trình soạn thảo nào để thay đổi nội dung file ```.config``` có trong thư mục crosstool-ng.

Crosstool-ng đi kèm rất nhiều bộ config có sẵn, trong đó có cả thiết lập để build toolchain cho Raspberry Pi 2,3,4. Do đó ta sẽ sử dụng thiết lập này để làm gốc, sau đó sẽ tùy chỉnh sao cho phù hợp nhất với nhu cầu.

Để hiện các config mẫu đi kèm, ta dùng câu lệnh:

```bash
    ./ct-ng list-samples
```

Sau khi dùng câu lệnh trên ta sẽ thấy có một config có tên là: armv8-rpi3-linux-gnueabihf. Đây sẽ là config nền mà ta sử dụng trong bài viết này. Với Raspberry Pi 4 thì cũng sẽ tương tự.

```bash
    ./ct-ng armv8-rpi3-linux-gnueabihf
    ./ct-ng menuconfig
```

Sau khi dùng câu lệnh trên, cửa sổ config sẽ mở ra với giao diện dưới đây:

[comment]: #(Thêm ảnh)

Giờ ta tiến hành sửa. Trong mục ```Paths and mics options```, ta ta sé tick vào mục ```Debug crosstool-ng```, rồi tiếp tục tick vào ```Save intermediate steps```. Tính năng này sẽ giúp lưu lại tiến trình build, để đề phòng trường hợp gặp lỗi thì ta sẽ không phải chạy lại toàn bộ quá trình build.

Tùy chọn ```Local tarball directory``` là thư mục chứa tarball mã nguồn của từng thành phần được tải về trong quá trình build. Tùy chọn này đang có giá trị mặc định là ```$HOME/src```, có thể sửa tùy ý. 

**Lưu ý, để tarball được lưu trong đường dẫn trên, ta cần phải tạo thủ công thư mục tương ứng, nếu không, sau khi quá trình build kết thúc, tarball sẽ bị xóa toàn bộ và sẽ cần phải tải mới trong lần build tiếp theo.**

Sau đó chọn ```Exit``` để thoát ra menu ngoài. Chọn mục ```Operating System```. Trong mục này ta sẽ đổi đường dẫn tới source của linux thành phiên bản do Raspberry cung cấp trên Github. Sửa option ```Source of linux(Released tarball)``` thành ```Vendor/custom repository```.

Sửa option ```Repository URL``` thành: 

```text
    https://github.com/raspberrypi/linux
```

Sau đó sửa option ```Branch/tag/ to check out``` thành:

```text
    rpi-5.15.y
```

Ở option ```Version of linux```, sửa thành ```5.15.23```. Sau đó, chọn ```Exit``` để trở về menu bên ngoài.

Vào mục ```C-library```, như đã giới thiệu thì ta phải làm sao để phiên bản thư viện C có trên thiết bị trùng hoặc cao hơn toolchain. Do đó, trong mục này, ta sẽ sửa tùy chọn ```Version of glibc``` thành ```2.31```. Sau khi sửa xong, chọn ```Exit``` để vể menu bên ngoài.

Tiếp theo, ta vào mục ```C Compiler```. Trong đây ta sẽ sửa tùy chọn ```gcc extra config``` thành:

```text
    --enable-multiarch
```

Bước trên sẽ yêu cầu trình biên dịch phải hỗ trợ multiarch, giúp việc tìm đường dẫn tới các thư viện trong sysroot trở nên dễ dàng và chính xác hơn. Sau khi sửa xong, chọn ```Exit``` để về menu gốc.

## Build

Ở menu gốc, tiếp tục chọn ```Exit```. Khi ấy chương trình sẽ hỏi xem có muốn lưu config vừa rồi không, ta chọn ```Yes```. Khi này các thiết lập mới nhất đã được lưu vào file ```.config```.

Ta sẽ dùng bắt đầu quá trình build. Tùy vào cấu hình máy, quá trình build có thể diễn ra trong khoảng từ 15 - 30 phút.

```bash
    ./ct-ng build
```

Toolchain khi build xong sẽ được đặt trong thư mục ```$HOME/x-tools/armv8-rpi3-linux-gnueabihf```