# Linux Support Kit

Bộ script cài đặt và cấu hình máy Linux cho remote desktop qua XRDP.

## CachyOS

### SIC v1 — CachyOS đã cài

Chạy bằng user thường có quyền `sudo`:

```sh
sh -c 'f=$(mktemp)||exit 1;trap "rm -f -- \"\$f\"" 0 2 15;curl -fsSL https://raw.githubusercontent.com/nhndev110/linux-support-kit-release/main/setup-cachyos -o "$f"&&chmod +x "$f"&&"$f"'
```

> File tạm tự xóa khi script kết thúc, kể cả khi cài đặt dừng do lỗi.

### SIC v2 — cài mới từ live ISO

Chạy bằng `root` trong CachyOS live ISO:

```bash
curl -fsSLo start https://raw.githubusercontent.com/nhndev110/linux-support-kit-release/refs/heads/main/install-fast-cachyos/start && chmod +x start && sudo ./start
```

> ⚠️ Cấu hình mặc định cài lên `/dev/nvme0n1`. Kiểm tra đúng ổ trước khi tiếp tục.

## Debian 13

Chạy bằng `root` hoặc user có `sudo`:

```bash
curl -fsSL https://raw.githubusercontent.com/nhndev110/linux-support-kit-release/refs/heads/main/setup-debian13 -o setup-debian13 && chmod +x setup-debian13 && ./setup-debian13
```

Hoặc dùng `wget`:

```bash
wget -qO setup-debian13 https://raw.githubusercontent.com/nhndev110/linux-support-kit-release/refs/heads/main/setup-debian13 && chmod +x setup-debian13 && ./setup-debian13
```

## Menu xử lý sự cố

Chạy trên máy đã cài:

```sh
sh -c 'f=$(mktemp)||exit 1;trap "rm -f -- \"\$f\"" 0 2 15;curl -fsSL https://raw.githubusercontent.com/nhndev110/linux-support-kit-release/main/scripts-support-kit -o "$f"&&chmod +x "$f"&&"$f"'
```

> ⚠️ Khi chọn `q`, menu tự đóng shell hiện tại để xóa history trong RAM. Chạy lệnh
> trong terminal hoặc phiên SSH riêng.

Xem chức năng và hướng dẫn tại [Support-Linux.md](Support-Linux.md).
