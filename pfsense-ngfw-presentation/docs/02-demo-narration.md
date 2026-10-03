# Lời dẫn video demo (4:30)

3 cảnh, mỗi cảnh khoảng 1:30. Nếu video có thứ tự khác, đổi cảnh nhưng giữ thời lượng.

| Cảnh | Nói gì khi chiếu | Điểm cần chỉ vào |
|---|---|---|
| 1. Chặn mạng xã hội (DNSBL) | "Từ PC1 ở VLAN 10, truy cập facebook.com bị chặn và chuyển về IP ảo." | Trình duyệt bị chặn; dashboard DNSBL hiện facebook, telegram, tiktok |
| 2. Tấn công từ Kali (Suricata) | "Kali ở LAN 2 giả lập tấn công vào mạng nội bộ, Suricata phát hiện và ghi alert/drop." | Dòng alert trong Suricata; tên rule (signature) |
| 3. Multi-WAN failover | "Ngắt WAN 1, ping từ máy người dùng vẫn chạy, lưu lượng sang WAN 2." | Trạng thái gateway chuyển Offline/Online; số gói ping rớt |

## Quy tắc khi dẫn

1. Nói một câu trước mỗi cảnh: cảnh này chứng minh điều gì.
2. Im lặng 3-5 giây khi kết quả hiện.
3. Chỉ vào đúng dòng kết quả rồi mới giải thích.
