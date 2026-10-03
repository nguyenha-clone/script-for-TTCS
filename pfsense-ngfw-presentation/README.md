# pfSense NGFW cho doanh nghiệp vừa và nhỏ: kịch bản thuyết trình

Kịch bản thuyết trình 20 phút (thuyết trình + video demo + hỏi đáp) cho báo cáo học phần **Thực tập cơ sở**: *Nghiên cứu tường lửa thế hệ mới pfSense và ứng dụng trong doanh nghiệp vừa và nhỏ*.

- Trường: Học viện Kỹ thuật Mật mã (KMA), Khoa An toàn thông tin
- Giảng viên hướng dẫn: Ths. Lê Thị Hồng Vân
- Sinh viên thực hiện: Nguyễn Hoàng Giang (AT210517), Nguyễn Mạnh Hà (AT210518), Lê Duy Hưởng (AT210524)

## Cấu trúc repo

| File | Nội dung |
|---|---|
| [docs/01-script.md](docs/01-script.md) | Script thuyết trình 10:30, chia theo khối slide |
| [docs/02-demo-narration.md](docs/02-demo-narration.md) | Lời dẫn cho video demo 4:30 (3 cảnh) |
| [docs/03-qa.md](docs/03-qa.md) | 6 câu hỏi đáp dự kiến, mỗi câu trả lời tối đa 3 câu |
| [docs/04-slide-fixes.md](docs/04-slide-fixes.md) | Lỗi trong slide cần sửa trước khi trình bày |
| `slides/TTCS.pdf` | File slide gốc (thêm vào nếu muốn) |

## Phân bổ thời gian (20 phút)

| # | Phần | Thời lượng | Slide |
|---|---|---|---|
| 1 | Mở đầu | 0:30 | 1-2 |
| 2 | Chương 1: Tổng quan NGFW | 2:20 | 4-10 |
| 3 | Chương 2: pfSense | 2:45 | 12-18 |
| 4 | Chương 3: Giải pháp | 4:15 | 20-28 |
| 5 | Kết luận | 0:40 | 28 |
| 6 | Video demo | 4:30 | |
| 7 | Hỏi đáp | 5:00 | |
| | **Tổng** | **20:00** | |

Slide chuyển chương (3, 11, 19) được bỏ qua; câu chuyển nằm ở cuối đoạn trước.

## Cách dùng

1. Đọc `docs/04-slide-fixes.md` và sửa slide trước.
2. Đọc `docs/01-script.md` thành tiếng, bấm giờ từng khối.
3. Quay video demo theo `docs/02-demo-narration.md`.
4. Luyện `docs/03-qa.md` với một người đóng vai giảng viên.

## Tóm tắt nội dung

- **Chương 1:** tường lửa, phân vùng mạng, phân loại theo OSI và hình thức triển khai, lý do NGFW ra đời, 4 yêu cầu bảo mật của SME.
- **Chương 2:** pfSense (FreeBSD, PF, NAT, VLAN), mở rộng thành NGFW bằng Suricata (IDS/IPS, DPI) và pfBlockerNG (IP, GeoIP, DNSBL), ưu và nhược điểm.
- **Chương 3:** ba mô hình triển khai trên GNS3: (1) Router-on-a-Stick + VLAN + Multi-WAN, (2) phòng thủ đa lớp pfBlockerNG + Suricata, (3) chặn mạng xã hội bằng DNSBL + QoS.
