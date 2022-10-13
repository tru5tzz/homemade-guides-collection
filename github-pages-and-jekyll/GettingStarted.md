# Mình đã bắt đầu với Github Pages và Jekyll như thế nào

## Tải Ruby cho Windows

Mở WSL2 lên và gõ lệnh:

```bash
    sudo apt-get install ruby-full
```

Câu lệnh bên trên sẽ cài Ruby vào máy, theo đó `bundle` và `gem` cũng sẽ được cài luôn, chỉ cần sử dụng thôi.

Sau đó update `gem`:

```bash
    sudo gem update
```

Nếu không dùng sudo thì sẽ gặp vấn đề với quyền (permission)

Sau đó là cài đặt Jekyll và Bundler với `gem`:

```bash
    sudo gem install jekyll bundler
```

## Link tài liệu:

1. [Ruby Install](https://www.ruby-lang.org/en/documentation/installation/#apt)
2. [Jekyll Install](https://jekyllrb.com/docs/installation/windows/)
