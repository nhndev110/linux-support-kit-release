# Linux Support Kit

Bộ script và tài liệu hỗ trợ cài đặt, cấu hình nhanh máy chủ Linux.

Xem thêm các lệnh xử lý sự cố tại [Support-Linux.md](Support-Linux.md).

---

## Script Cài Linux

### CachyOS

**_SIC v1_** - chạy lệnh sau khi cài xong

```bash
curl -fsSL https://raw.githubusercontent.com/nhndev110/linux-support-kit-release/refs/heads/main/setup-cachyos -o setup-cachyos && chmod +x setup-cachyos && ./setup-cachyos
```

**_SIC v2_** - chạy lệnh trong live ISO

```bash
curl -fsSLo start https://raw.githubusercontent.com/nhndev110/linux-support-kit-release/refs/heads/main/install-fast-cachyos/start && chmod +x start && sudo ./start
```

### Debian

> ⚠️ Chạy bằng `root`: đặt mật khẩu root (`sudo passwd root`) rồi vào phiên root (`su -`) trước khi chạy script.

Tải và chạy script cài đặt (dùng `curl` **hoặc** `wget`):

```bash
curl -fsSL https://raw.githubusercontent.com/nhndev110/linux-support-kit-release/refs/heads/main/setup-debian13 -o setup-debian13 && chmod +x setup-debian13 && ./setup-debian13
```

```bash
wget -qO setup-debian13 https://raw.githubusercontent.com/nhndev110/linux-support-kit-release/refs/heads/main/setup-debian13 && chmod +x setup-debian13 && ./setup-debian13
```

> 💡 Xem chi tiết **script làm những gì** tại [Support-Linux.md](Support-Linux.md#script-setup-nhanh-linux--script-làm-những-gì).

---

## Menu xử lý sự cố

Tải và chạy script menu:

```bash
curl -fsSL https://raw.githubusercontent.com/nhndev110/linux-support-kit-release/refs/heads/main/scripts-support-kit -o support-kit && chmod +x support-kit && ./support-kit
```

> 💡 Xem danh sách **các chức năng** của menu tại [Support-Linux.md](Support-Linux.md#menu-xử-lý-sự-cố).
