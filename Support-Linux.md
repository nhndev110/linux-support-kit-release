# Hỗ trợ Linux

Tài liệu tra cứu nhanh cho các chức năng trong Linux Support Kit.

---

## Menu xử lý sự cố

> 💡 Lệnh tải và chạy menu có tại [README.md](README.md#menu-xử-lý-sự-cố).

`scripts-support-kit` có các chức năng sau:

Lệnh một dòng trong `README.md` gọi wrapper `sh -c` tương thích với Bash và Fish,
tải binary vào file tạm do `mktemp` tạo rồi tự xóa file khi kết thúc. Vì vậy không
để lại file `support-kit` trong thư mục hiện tại hoặc thư mục home.

Khi thoát menu, script bỏ qua PID của wrapper `sh -c` không tương tác và tìm shell
Fish/Bash thật đang giữ history. Script tự đóng shell đó, không hỏi xác nhận, để xóa
history trong RAM. Chạy menu trong terminal hoặc phiên SSH riêng vì phiên hiện tại
sẽ bị đóng khi chọn `q`.

1. **Cấu hình DNS (NetworkManager)** — chọn Mikrotik, Google, Cloudflare, VNPT,
   Viettel hoặc tự nhập DNS; áp vào connection đang có default route.
2. **Xóa chữ ký và bảng phân vùng ổ đĩa** — chọn một block device, xác nhận đúng
   tên ổ, rồi xóa filesystem signature và tạo bảng phân vùng MBR mới.
3. **Đổi mật khẩu user + root** — đặt cùng một mật khẩu mới cho user hiện tại và
   `root`.
4. **Cấu hình mạng + port XRDP** — nhập IP `/24`, DNS và port XRDP; script hiển thị
   cấu hình sắp áp dụng để xác nhận trước khi thay đổi.
5. **Cài lại Accops Client** — nhập PIN và tên thiết bị, xóa cấu hình Accops cũ,
   cài lại client rồi reboot.
6. **Cài SCADA agent** — tải agent từ `scada.tpservers.com` và cài với cấu hình
   SCADA của bộ script.

---

## Xóa chữ ký và bảng phân vùng ổ đĩa

> ⚠️ Thao tác này có thể làm ổ không thể boot và mất quyền truy cập dữ liệu. Kiểm tra kỹ tên ổ bằng `lsblk`.

Menu yêu cầu nhập tên ổ, ví dụ `nvme0n1`, sau đó phải gõ lại chính xác tên đó để xác
nhận. Source hiện chạy các lệnh sau:

```bash
sudo wipefs -a /dev/nvme0n1
sudo parted /dev/nvme0n1 mklabel msdos
```

Nó không chạy `swapoff`, `vgchange`, `dmsetup` hoặc `sgdisk --zap-all`. Nếu ổ đang
được dùng bởi swap, LVM hay device mapper, cần xử lý các thành phần đó thủ công trước
khi dùng menu.

---

## Đổi mật khẩu cho user và root

Menu đặt cùng một mật khẩu cho user đang chạy menu và `root`. Có thể thực hiện thủ
công bằng:

```bash
set NEWPASS "<Mật khẩu>"; printf '%s:%s\n%s:%s\n' "$USER" "$NEWPASS" root "$NEWPASS" | sudo chpasswd
```

---

## Support Farmers V5 (Accops Client)

Khởi động lại dịch vụ:

```bash
sudo systemctl restart accops-client
```

Theo dõi log thời gian thực:

```bash
sudo journalctl -fu accops-client
```

Cài lại từ đầu:

```bash
sudo systemctl stop accops-client 2>/dev/null || true && sudo rm -rf ~/accops /etc/accops && curl -fsSL https://accountops.org/install | sudo bash -s -- --pin <mã pin> --name "<tên thiết bị>" && sudo reboot
```

### Trạng thái phiên

- **assigned** — tổng số phiên được cấp cho máy.
- **active** — phiên đang chạy bình thường.
- **launching** — phiên đang khởi động.
- **face** — phiên chờ xác minh khuôn mặt.
- **captcha** — phiên bị chặn bởi captcha.
- **idle** — phiên được cấp nhưng chưa chạy.
- **backoff** — request lỗi và client đang tăng thời gian thử lại.

---

## Tắt máy và khởi động lại

```bash
sudo shutdown -h now
sudo reboot now
```

---

## Script Setup Nhanh Linux — Script làm những gì?

> 💡 Lệnh tải và chạy CachyOS, Debian hoặc live ISO có tại [README.md](README.md).

Các script setup biến máy vừa cài OS thành máy remote desktop qua XRDP. Chúng hỏi
port, desktop, IP tĩnh và mật khẩu trước khi chạy các bước tự động; khi thành công,
script tự xóa và reboot.

### Chức năng chung

- Cập nhật hệ thống.
- Cài và bật XRDP.
- Chọn desktop cho phiên XRDP và đổi port, mặc định `3389`.
- Tạo file session: `~/.xinitrc` trên CachyOS hoặc `~/.xsession` trên Debian.
- Tắt DPMS/screensaver cho phiên XRDP.
- Hiển thị network hiện tại, có thể đặt IP tĩnh `/24`, gateway dạng `.1` và DNS.
- Có thể đổi mật khẩu user + root.
- Tắt sleep, hibernate và khóa màn hình theo desktop.
- Tải ảnh nền chuẩn tới `/usr/share/backgrounds/tp-wallpaper.jpg` khi desktop hỗ trợ.
- Thử cài SCADA agent.

### CachyOS đã cài (`setup-cachyos`)

- Chạy bằng user thường; dùng `sudo` cho thay đổi hệ thống và tự cài `paru` nếu thiếu.
- Lệnh SIC v1 trong `README.md` tải binary vào file tạm; wrapper tự xóa file cả khi
  script dừng do lỗi. Khi chạy source trực tiếp, script chỉ tự xóa sau khi thành công.
- Cài `xrdp` và `xorgxrdp` qua AUR, tạo cert XRDP và tắt UFW nếu UFW tồn tại.
- Mặc định dùng KDE Plasma X11.
- Tắt faillock bằng `deny = 0` để tránh khóa user do nhập sai qua RDP.

### Debian 13 đã cài (`setup-debian13`)

- Chạy bằng root/sudo; tự tìm user thường để ghi đúng home.
- Dùng apt để cài `xrdp`, `dbus-x11`, bật kho `contrib`, `non-free` và
  `non-free-firmware`.
- Cài NVIDIA proprietary qua DKMS và cảnh báo khi Secure Boot bật.
- Tạo polkit rule cho color profile và dùng `dbus-launch` với Cinnamon, Plasma,
  GNOME để giảm lỗi phiên XRDP.
- Mặc định dùng Cinnamon.

### Cài nhanh CachyOS từ live ISO (`start`)

- Chạy bằng `root` trong CachyOS live ISO; tự đặt `Asia/Ho_Chi_Minh` cho live ISO
  và ghi timezone này vào `/root/settings.json`.
- Có thể nhận cấu hình theo số máy từ API. Lỗi API không chặn cài đặt; khi đó
  `configure-system` sẽ hỏi cấu hình thủ công ở lần boot đầu.
- Áp thử IP tĩnh lên live ISO để installer có mạng; nếu kiểm tra Internet thất bại,
  script trả connection về DHCP rồi tiếp tục.
- Tải các artifact release `/root/settings.json`, `post-install` và
  `configure-system`. Nếu installer hỗ trợ `--config`, script truyền thẳng file;
  bản cũ thì chọn **Load config** trong TUI.
- Làm tươi mirror và pacman database, kiểm chữ ký `cachyos.db`/`.sig` trên CDN
  trước khi mở installer. Nếu CDN lệch chữ ký, script chờ tối đa 10 phút.
- Chỉ coi cài thành công khi `post-install` ghi `=== Xong ===`. Lỗi mirror tạm thời
  được thử lại tối đa 3 lần; lỗi khác dừng và không reboot.
- Khi thành công, `configure-system` chạy ở boot đầu để hoàn tất XRDP, desktop,
  network, SCADA agent và driver NVIDIA khi có card phù hợp.
- Log: `/tmp/cachyos-install.log`, các bản retry có hậu tố `.1`, `.2`, `.3`, và
  `/tmp/post-install.log` trên live ISO.

---

## Lỗi chữ ký CachyOS khi cài từ live ISO

Triệu chứng trong log installer:

```text
error: cachyos: signature from "CachyOS <admin@cachyos.org>" is invalid
error: failed to synchronize all databases (unexpected error)
[  9.1%] Base install failed
```

Đây là lỗi tạm thời của mirror/CDN: vài phút sau khi repo cập nhật, `cachyos.db`
và `cachyos.db.sig` được trả về từ hai phiên bản khác nhau. Không phải keyring cũ
hay đồng hồ sai (khi đó pacman báo `unknown trust` hoặc `key expired`).

`start` xử lý tự động: kiểm chữ ký trên CDN trước khi mở installer, và khi installer
chết vì lỗi này thì chờ mirror khớp lại rồi cài lại, tối đa 3 lần. Nếu vẫn lỗi:

1. Đợi vài phút rồi chạy lại `sudo ./start <số máy>`.
2. Kiểm tay trên live ISO:

```bash
curl -fsSO https://cdn77.cachyos.org/repo/x86_64/cachyos/cachyos.db
curl -fsSO https://cdn77.cachyos.org/repo/x86_64/cachyos/cachyos.db.sig
pacman-key --verify cachyos.db.sig cachyos.db
```

3. Chỉ khi `pacman-key --list-keys F3B607488DB35A47` không có key mới cần
   `pacman-key --init && pacman-key --populate archlinux cachyos`, hoặc tải ISO mới.

Log để đọc: `/tmp/cachyos-install.log` (bản của lần lỗi trước được giữ ở
`/tmp/cachyos-install.log.<lần>`) và `/tmp/post-install.log`.
