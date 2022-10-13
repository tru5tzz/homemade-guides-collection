# Giới thiệu chung

Page này sẽ được viết về chủ để có liên quan tới việc crossbuild Qt cho Raspberry Pi. Phiên bản Qt được hướng tới sẽ là bản 6 với nhiều thay đổi mới phức tạp hơn.

Có thể nói đây sẽ là một hướng dẫn hiếm hoi về cách build Qt 6 trên Raspberry Pi. Phiên bản Pi không quan trọng vì quy trình sẽ gần như giống nhau toàn bộ. Điểm nổi bật của bài hướng dẫn này chính là nằm ở việc sử dụng Qt phiên bản 6, mới hơn về mặt framework lẫn build system. Đi cùng với phiên bản này là một vài thay đổi lớn về cách thức build cross Qt so với thế hệ tiền nhiệm.

## Nội dung chính

- Giải thích sơ lược về toolchain, vai trò của nó trong quá trình buildcross nói chung
  - Khái niệm (máy host, target)
  - Thành phần
  - Sysroot là gì?
  - Multiarch trên hệ điều hành dòng Debian
- Chuẩn bị môi trường built toolchain
- Build cross toolchain cho Raspberry Pi
- Chuẩn bị môi trường build Qt 6
  - Raspberry Pi
  - Máy host
- Build native cho máy host
- Crossbuild cho Raspberry Pi
- Đường dẫn tham khảo

## Đường dẫn tham khảo
- [Toolchain - eLinux.org](https://elinux.org/Toolchains)
- [Cross compiler - Wikipedia](https://www.wikiwand.com/en/Cross_compiler)
- [Build Qt6 from Git - Qt Wiki](https://wiki.qt.io/Building_Qt_6_from_Git)
- [Qt Official Docs - Qt](https://doc.qt.io/)
- [Qt6 QML Book - Qt](https://www.qt.io/product/qt6/qml-book)
- [Cross-compiling Qt 5.15.0 for Raspberry Pi 4 - UvinduW](https://github.com/UvinduW/Cross-Compiling-Qt-for-Raspberry-Pi-4)
- [Cross-compile Qt 6 for Raspberry Pi - Qt Wiki](https://wiki.qt.io/Cross-Compile_Qt_6_for_Raspberry_Pi)
- [Build toolchain for Raspberry Pi with Crosstool-NG - Raspberry Pi Forums](https://forums.raspberrypi.com/viewtopic.php?t=280707)
- [How to build a GCC Cross-compiler](https://preshing.com/20141119/how-to-build-a-gcc-cross-compiler/)

## Mục lục

1. [Toolchain là gì?](./ToolchainDefinition.md)
2. [Cách build toolchain nhanh và đơn giản với crosstool-ng](ToolchainCompiling.md)