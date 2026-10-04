# Cấu trúc chốt Chương 3 và Chương 4

Báo cáo thực tập cơ sở: **Nghiên cứu tường lửa thế hệ mới pfSense và ứng dụng trong doanh nghiệp vừa và nhỏ**.
Học viện Kỹ thuật Mật mã (KMA), Khoa An toàn thông tin. Giảng viên hướng dẫn: ThS. Lê Thị Hồng Vân.

Tài liệu này là **khung viết báo cáo, làm slide và quay video demo** cho Chương 3 và Chương 4.

---

## 1. Bức tranh tổng thể

| Chương | Vai trò | Nội dung | Có số đo? |
|---|---|---|---|
| 1 | Cơ sở lý thuyết | Nguy cơ ATTT, firewall, NGFW, nhu cầu SME | Không |
| 2 | Công nghệ | pfSense, kiến trúc, phần cứng, Suricata, pfBlockerNG, ưu/nhược so với giải pháp thương mại | Không |
| **3** | **Thiết kế + cấu hình** | 3 mô hình triển khai | **Không** |
| **4** | **Kiểm chứng + đánh giá** | 2 thực nghiệm + đánh giá tổng kết | **Có** |

Mạch logic: Chương 1-2 trả lời "cần gì, pfSense có gì" → Chương 3 trả lời "dựng thế nào" → Chương 4 trả lời "chạy có đúng không, đạt NGFW đến đâu".

### Ranh giới Chương 3 và Chương 4

| Nội dung | Chương 3 | Chương 4 |
|---|---|---|
| Bài toán, sơ đồ, địa chỉ IP/VLAN | Có | Không, chỉ dẫn "Hình 3.1" |
| Các bước cấu hình (VLAN, Gateway Group, Suricata, DNSBL, Queue) | Có | Không lặp |
| Cài đặt pfSense từ ISO | Không | 4.2 |
| Kịch bản test, lệnh test, kết quả mong đợi so với thực tế | Không | 4.3, 4.4 |
| Số liệu đo | Không | 4.3, 4.4, 4.5 |
| Nhận xét đạt/không đạt, đối chiếu NGFW | Không | 4.5 |

### Phạm vi đã chốt

- Traffic Shaper (QoS) **chỉ cấu hình** ở mục 3.3.3, **không đo** hiệu quả.
- Suricata dùng ở chế độ **IDS/Legacy**. **Inline IPS chưa triển khai** trong phạm vi đề tài.
- **Không đo throughput bằng iperf3.** Chỉ đánh giá CPU, RAM, latency, packet loss.
- Số liệu chưa đo ghi `[...]`. Không tự điền số.

---

## 2. Mô hình mạng (tham chiếu Hình 3.1)

| Giao diện/VLAN | Subnet | Vai trò |
|---|---|---|
| WAN (vtnet0) | 192.168.58.147/24 | Ra Internet qua NAT1 (WAN chính) |
| LAN (vtnet1) | 192.168.1.1/24 | Cổng trunk 802.1Q tới switch Cisco |
| WAN2 (vtnet2) | 203.162.1.1/24 | Đường dự phòng, nối Router R2 |
| VLAN 10 | 192.168.10.0/24 | Nhân viên văn phòng (PC1) |
| VLAN 20 | 192.168.20.0/24 | Khách (PC2) |
| VLAN 30 - MANAGEMENT | 192.168.30.0/24 | Máy chủ WindowsServer2025-1 và quản trị |
| LAN 2 | 172.128.1.0/24 | Môi trường kiểm thử: Kali-1, PC3, R2 |

Nền tảng lab: GNS3, pfSense 2.7.2. `[ghi thêm VMware nếu thực tế có dùng]`

---

## 3. Chương 3. Ứng dụng pfSense trong doanh nghiệp vừa và nhỏ (SME)

> Chương này chỉ trình bày **thiết kế và cách cấu hình**. Kết quả kiểm chứng và số liệu nằm ở Chương 4.

### 3.1. Mô hình 1: pfSense làm Gateway và Tường lửa trung tâm

| Mục | Nội dung |
|---|---|
| 3.1.1 | Phân tích bài toán thực tế: định tuyến ổn định, dự phòng đường truyền, cô lập mạng bằng VLAN |
| 3.1.2 | Kiến trúc Router-on-a-Stick, phân hoạch VLAN 10/20/30, môi trường kiểm thử LAN 2 (Hình 3.1) |
| 3.1.3 | Triển khai cấu hình chi tiết |
| 3.1.3a | Thiết lập VLAN: parent interface vtnet1, VLAN Tag, gán interface, IP gateway, DHCP (Hình 3.2) |
| 3.1.3b | Multi-WAN: khai báo WAN2, kiểm tra Gateway, tạo Gateway Group `WAN_GROUP` (Tier 1/Tier 2), áp dụng vào Firewall Rules (Hình 3.3) |

### 3.2. Mô hình 2: Phòng thủ đa lớp bằng pfBlockerNG và Suricata

| Mục | Nội dung |
|---|---|
| 3.2.1 | Phân tích yêu cầu bảo vệ máy chủ VLAN 30 |
| 3.2.2 | Lớp 1: pfBlockerNG. IPv4 Feeds (Abuse.ch Feodo, Spamhaus DROP), Action Deny Both, Pass List chống false positive, DNSBL chống phishing (Hình 3.4a, 3.4b) |
| 3.2.3 | Lớp 2: Suricata. Cài trên interface VLAN 30, chế độ **Legacy (IDS)**, ruleset ET Open (Hình 3.5a, 3.5b) |
| 3.2.4 | Thứ tự Firewall Rules: pfBlockerNG xếp trên cùng để chặn IP độc hại trước khi Suricata phân tích |

Ghi chú nhất quán: Hình 3.5 phải chụp với **IPS Mode = Legacy**, **Block Offenders tắt**. Thêm một câu: "Inline Mode chưa triển khai kiểm thử trong phạm vi đề tài."

### 3.3. Mô hình 3: Kiểm soát truy cập Internet và tối ưu băng thông

| Mục | Nội dung |
|---|---|
| 3.3.1 | Bài toán chặn mạng xã hội trong giờ làm việc và ưu tiên lưu lượng quan trọng |
| 3.3.2 | Lọc nội dung bằng pfBlockerNG DNSBL |
| 3.3.2a | Bật DNS Resolver (Unbound) trên WAN, LAN, VLAN 10, 20, 30 |
| 3.3.2b | Tạo nhóm `Block_SocialNetWork`, nhập Custom_List (Facebook, TikTok, Instagram, Telegram) |
| 3.3.2c | Force Update, bật DNSBL, Virtual IP `10.10.10.1` |
| 3.3.2d | Chống né qua DoT (chặn port 853) và ép máy khách dùng DNS pfSense (chặn port 53 ra ngoài) |
| 3.3.2e | Cách kiểm tra (lệnh `nslookup`); kết quả đo nằm ở mục 4.3.4 |
| 3.3.3 | Traffic Shaper (QoS): **chỉ trình bày cấu hình, không đo hiệu quả** |
| 3.3.3a | Multiple LAN/WAN Wizard tạo cấu trúc Interface (Hình 3.9) |
| 3.3.3b | Hàng đợi: qVoIP 40%, qDefault 50%, qP2P dưới 10% (Hình 3.10) |

Mục 3.4 cũ (Đánh giá kết quả triển khai) **đã chuyển sang Chương 4**.

---

## 4. Chương 4. Thực nghiệm và đánh giá kết quả

### Đoạn mở đầu chương

> Chương này kiểm chứng mô hình đã thiết kế ở Chương 3 trên môi trường lab ảo hóa GNS3 `[ghi thêm VMware nếu có]`. Các chức năng định tuyến, firewall, dự phòng đường truyền, lọc DNS, phát hiện xâm nhập và chặn theo danh sách đe dọa được đánh giá bằng khả năng hoạt động thực tế, log, gói tin bắt được và số liệu đo. Các bước cấu hình chi tiết không lặp lại mà được dẫn chiếu về mục tương ứng ở Chương 3.

### 4.1. Môi trường thực nghiệm và phương pháp đánh giá

| Mục | Nội dung | Hình/bảng |
|---|---|---|
| 4.1.1 | Mô hình thực nghiệm, dẫn Hình 3.1, không vẽ lại | Không |
| 4.1.2 | Cấu hình các node: pfSense, Kali-1, PC1, PC2, WindowsServer2025-1, R2 | Bảng 4.1 |
| 4.1.3 | Công cụ: `ping`, `nslookup`, `nmap`, `hping3`, Wireshark, pfSense Firewall/State/Logs, Suricata Alerts, pfBlockerNG Logs | Một đoạn liệt kê |
| 4.1.4 | Tiêu chí đánh giá. **Chốt ngưỡng PASS trước khi đo** | Bảng 4.2 |

**Bảng 4.1 (mẫu):**

| Node | Vai trò | CPU | RAM | Disk | Interface/IP |
|---|---|---:|---:|---:|---|
| pfSense 2.7.2 | Firewall/Router | `[...]` | `[...]` | `[...]` | `[...]` |
| Kali-1 | Máy tấn công/kiểm thử | `[...]` | `[...]` | `[...]` | `[...]` |
| PC1 | Client VLAN 10 | `[...]` | `[...]` | `[...]` | `[...]` |
| PC2 | Client VLAN 20 | `[...]` | `[...]` | `[...]` | `[...]` |
| WindowsServer2025-1 | Server VLAN 30 | `[...]` | `[...]` | `[...]` | `[...]` |
| R2 | Router LAN 2 | `[...]` | `[...]` | `[...]` | `[...]` |

**Bảng 4.2 (tiêu chí):**

| Chức năng | Phương pháp | Kết quả kỳ vọng | Ngưỡng PASS |
|---|---|---|---|
| Routing | ping | Đúng policy | Actual = Expected |
| Firewall | ping/nmap | Traffic trái phép bị chặn | Có log block khớp rule |
| Failover | Ngắt WAN1 | Chuyển sang WAN2 | Trung bình ≤ `[...]` s |
| DNSBL | nslookup/trình duyệt | Domain bị chặn | Trả về `10.10.10.1` |
| Suricata (IDS) | nmap/hping3 | Phát hiện và tạo alert | Có alert tương ứng |
| pfBlockerNG | Traffic tới IP thuộc feed | Bị chặn | Có log block |

### 4.2. Cài đặt pfSense và kiểm tra ban đầu

| Mục | Nội dung | Hình | PASS khi |
|---|---|---|---|
| 4.2.1 | Tạo và cài đặt VM pfSense 2.7.2: CPU/RAM/Disk, filesystem (ghi đúng thực tế), cài từ ISO | Hình 4.1 (gộp) | VM boot được vào console |
| 4.2.2 | Gán và kiểm tra interface WAN, WAN2, LAN | Hình 4.1 | Interface Up, đúng IP |
| 4.2.3 | Đăng nhập WebGUI, **đổi mật khẩu admin mặc định**, kiểm tra Dashboard | Hình 4.1 | Cảnh báo mật khẩu mặc định biến mất |
| 4.2.4 | Ping gateway, ping Internet từ pfSense, xác nhận sẵn sàng | Không | Loss 0% |

**Dừng trước bước tạo VLAN.** Cấu hình VLAN: xem mục 3.1.3a.

### 4.3. Thực nghiệm 1: Định tuyến, Failover và DNSBL

| Mục | Nội dung |
|---|---|
| 4.3.1 | Mục tiêu: routing, firewall policy, Multi-WAN/Failover, DNSBL |
| 4.3.2 | Kiểm thử định tuyến và firewall |
| 4.3.3 | Kiểm thử Multi-WAN/Failover |
| 4.3.4 | Kiểm thử DNSBL |
| 4.3.5 | Tổng hợp và kết luận thực nghiệm 1 |

#### 4.3.2. Định tuyến và firewall

1. Ping giữa các VLAN và tới gateway, đối chiếu với policy ở mục 3.1.3 và 3.2.4.
2. Với gói bị chặn: mở Status > System Logs > Firewall, xác nhận có dòng block khớp rule; kiểm tra State Table.

| Source | Destination | Expected | Actual | Result |
|---|---|---|---|---|
| PC1 | Gateway VLAN 10 | `[theo rule]` | `[...]` | PASS/FAIL |
| PC1 | PC2 | `[theo rule]` | `[...]` | PASS/FAIL |
| PC1 | Server VLAN 30 | `[theo rule]` | `[...]` | PASS/FAIL |
| PC2 | Server VLAN 30 | `[theo rule]` | `[...]` | PASS/FAIL |

Expected lấy từ rule đã cấu hình, không mặc định mọi VLAN ping được nhau.

#### 4.3.3. Multi-WAN/Failover

1. Kiểm tra WAN1/WAN2 tại Status > Gateways.
2. Từ PC1 chạy `ping -t 8.8.8.8`.
3. Ngắt WAN1 (vtnet0) trong GNS3.
4. Quan sát gateway chuyển sang WAN2, ghi số gói `Request timed out`.
5. Xác nhận Internet nối lại qua WAN2.
6. Khôi phục WAN1, kiểm tra trạng thái sau phục hồi.
7. Lặp **10 lần**.

| Lần | Thời gian failover |
|---:|---:|
| 1 | `[...]` s |
| ... | ... |
| 10 | `[...]` s |
| Trung bình | `[...]` s |
| Nhỏ nhất | `[...]` s |
| Lớn nhất | `[...]` s |

PASS khi trung bình ≤ ngưỡng ở Bảng 4.2 và Internet nối lại qua WAN2 cả 10 lần.

#### 4.3.4. DNSBL

| Phép thử | Lệnh/thao tác | Kết quả kỳ vọng | Thực tế |
|---|---|---|---|
| Truy vấn domain bị chặn | `nslookup facebook.com`, `tiktok.com`, `instagram.com`, `telegram.org` từ PC1, PC2 | Trả về `10.10.10.1` | `[...]` |
| Trình duyệt | Mở `https://facebook.com` | Trang chặn của pfBlockerNG, có log DNSBL | `[...]` |
| Né bằng DNS ngoài | `nslookup facebook.com 8.8.8.8` | Bị chặn bởi rule port 53 | `[...]` |
| Né bằng DoT | Truy vấn qua port 853 | Bị chặn | `[...]` |
| Né bằng DoH | `[nếu kiểm thử được]` | `[...]` | `[...]` |

Dẫn Hình 3.7, 3.8 cho kết quả chặn thành công, không chụp lại. DNSBL chỉ kiểm soát phân giải DNS; nếu DoH né được, ghi nhận là **hạn chế của DNSBL**, không phải lỗi của toàn bộ firewall.

#### 4.3.5. Tổng hợp thực nghiệm 1

| Chức năng | Tiêu chí | Kết quả | Đánh giá |
|---|---|---|---|
| Inter-VLAN Routing | `[...]` | `[...]` | PASS/FAIL |
| Firewall | `[...]` | `[...]` | PASS/FAIL |
| Multi-WAN | `[...]` | `[...]` | PASS/FAIL |
| DNSBL | `[...]` | `[...]` | PASS/Một phần/FAIL |

### 4.4. Thực nghiệm 2: Mô phỏng tấn công và phát hiện của Suricata (IDS)

Chỉ xét Suricata ở chế độ **IDS/Legacy**. Không triển khai Inline.

| Mục | Nội dung |
|---|---|
| 4.4.1 | Mục tiêu: phát hiện port scan, traffic bất thường/flood, tạo alert, ghi nhận source/destination/signature |
| 4.4.2 | Baseline khi chưa bật Suricata |
| 4.4.3 | Kiểm thử Suricata |
| 4.4.4 | Phân tích gói tin bằng Wireshark |
| 4.4.5 | Kiểm thử pfBlockerNG |
| 4.4.6 | Tổng hợp kết quả |
| 4.4.7 | Kết luận thực nghiệm 2 |

**Điều kiện tiên quyết** (kiểm tra trước khi đo): Kali (LAN 2) phải tới được server VLAN 30 qua WAN2 (port forward hoặc rule cho phép). Chạy `nmap` từ Kali, phải thấy ít nhất 1 cổng mở.

#### 4.4.2. Baseline (Suricata tắt)

```bash
nmap -p 21,80,443,445,3389,5985 <IP_server>
nmap -sV <IP_server>
hping3 ...   # trong lab kiểm soát, giới hạn số gói
```

Ghi: cổng mở, số gói gửi, số gói tới server. PASS khi traffic tới được server và **không có alert** (chứng minh alert ở bước sau do Suricata). Lặp 3 lần, lệch ≤ 10%.

#### 4.4.3. Suricata bật (Legacy, Block Offenders tắt)

Lặp đúng kịch bản baseline. Ghi: alert, signature, source IP, destination IP, protocol, timestamp, priority/severity.

Lưu ý: `hping3` flood thường không khớp signature ET Open mặc định. Nếu không có alert, ghi "không phát hiện bằng ruleset ET Open mặc định". Đó là kết quả hợp lệ, không sửa số.

| Chỉ số | Suricata tắt | Suricata bật |
|---|---:|---:|
| Số gói gửi | `[...]` | `[...]` |
| Số gói tới server | `[...]` | `[...]` |
| Số alert | 0 | `[...]` |

#### 4.4.4. Wireshark

Bắt trên cổng pfSense phía Kali, lọc `tcp.flags.syn==1 && tcp.flags.ack==0`. Ghi số gói SYN và kích thước gói. PASS khi thấy SYN từ Kali tới nhiều cổng.

#### 4.4.5. pfBlockerNG

1. Lấy một IP thuộc feed đã bật (Feodo hoặc Spamhaus).
2. Tạo traffic tới IP đó.
3. Xem Firewall Logs và pfBlockerNG Reports.

| Test | IP/Domain | Feed | Expected | Actual | Result |
|---|---|---|---|---|---|
| IP thuộc feed | `[...]` | `[...]` | Block | `[...]` | PASS/FAIL |

#### 4.4.6. Tổng hợp thực nghiệm 2

| Cơ chế | Kịch bản | Kết quả kỳ vọng | Kết quả thực tế | Đánh giá |
|---|---|---|---|---|
| Suricata IDS | nmap | Alert | `[...]` | PASS/FAIL |
| Suricata IDS | hping3 | Alert | `[...]` | PASS/FAIL |
| pfBlockerNG | IP thuộc feed | Block | `[...]` | PASS/FAIL |

### 4.5. Đánh giá tổng kết

| Mục | Nội dung |
|---|---|
| 4.5.1 | Đánh giá tài nguyên hệ thống (CPU, RAM, latency, packet loss). **Không đo throughput** |
| 4.5.2 | Đối chiếu với tiêu chí NGFW (mục 1.3.2) |
| 4.5.3 | Khuyến nghị cấu hình cho SME (dẫn bảng mục 2.1.3, không tự tạo số) |
| 4.5.4 | Hạn chế của đề tài |
| 4.5.5 | Tổng kết Chương 4 |

#### 4.5.1. Tài nguyên hệ thống

Đo trong **cùng một tải kiểm thử** (cùng `nmap`/`hping3`) ở cả 3 cấu hình. Latency: `ping -n 100` từ PC1 tới server.

| Cấu hình | CPU | RAM | Latency (TB/Min/Max) | Packet loss |
|---|---:|---:|---:|---:|
| Baseline | `[...]` | `[...]` | `[...]` | `[...]` |
| Suricata | `[...]` | `[...]` | `[...]` | `[...]` |
| Suricata + pfBlockerNG | `[...]` | `[...]` | `[...]` | `[...]` |

Mức tăng CPU/RAM chỉ phân tích, không đặt PASS/FAIL.

#### 4.5.2. Đối chiếu NGFW

Chỉ ghi "đáp ứng" cho tính năng **đã triển khai và kiểm thử**.

| Tiêu chí | Kết quả | Bằng chứng |
|---|---|---|
| Stateful Firewall | Đáp ứng | 4.3.2, 4.3.3 |
| DPI | Đáp ứng một phần | 4.4.3 |
| IDS | Đáp ứng | 4.4.3 |
| IPS (chặn trực tiếp) | Chưa triển khai trong phạm vi đề tài (Inline Mode) | Mục 4.5.4 |
| Threat Intelligence | Đáp ứng | 4.4.5 |
| Application Control | Đáp ứng một phần hoặc chưa triển khai | 4.3.4 (chặn theo domain) |
| TLS Inspection | Chưa triển khai trong phạm vi đề tài | Mục 4.5.4 |
| User-ID | Chưa triển khai trong phạm vi đề tài | Mục 4.5.4 |

#### 4.5.4. Hạn chế

- Môi trường lab ảo hóa, tài nguyên VM giới hạn, kết quả phụ thuộc máy chủ ảo hóa.
- Chưa kiểm thử tải lớn, chưa đo throughput.
- Chưa triển khai Inline IPS.
- Chưa triển khai TLS Inspection, User-ID.
- DNSBL có thể bị né bằng DoT/DoH nếu chưa kiểm soát.
- QoS chỉ được cấu hình, chưa đánh giá thực nghiệm.

---

## 5. Danh sách hình và bảng

| Loại | Nội dung | Mục |
|---|---|---|
| Hình 4.1 | Dashboard pfSense và trạng thái interface (gộp) | 4.2 |
| Hình 4.2 | Ping giữa VLAN và Firewall Log chặn (gộp) | 4.3.2 |
| Hình 4.3 | Status > Gateways khi chuyển sang WAN2 | 4.3.3 |
| Hình 4.4 | Thử né DNSBL bằng DNS ngoài/DoT | 4.3.4 |
| Hình 4.5 | Suricata Alerts của `nmap` | 4.4.3 |
| Hình 4.6 | Suricata Alerts hoặc log trống của `hping3` | 4.4.3 |
| Hình 4.7 | Wireshark SYN scan | 4.4.4 |
| Hình 4.8 | pfBlockerNG log block IP thuộc feed | 4.4.5 |
| Bảng 4.1-4.2 | Node; tiêu chí | 4.1 |
| Bảng 4.3-4.5 | Ma trận kết nối; failover 10 lần; DNSBL và né | 4.3 |
| Bảng 4.6 | Tổng hợp thực nghiệm 1 | 4.3.5 |
| Bảng 4.7-4.8 | Baseline so với Suricata bật; tổng hợp thực nghiệm 2 | 4.4 |
| Bảng 4.9-4.10 | CPU/RAM/latency/loss; đối chiếu NGFW | 4.5 |

Hình nào không chứng minh được một dòng PASS/FAIL cụ thể thì bỏ.

---

## 6. Phần trình bày (20 phút) và video demo

| Phần | Thời lượng |
|---|---|
| Mở đầu + Chương 1 | 2'15" |
| Chương 2 | 2'30" |
| Chương 3 | 3'30" |
| Chương 4 (4 slide + video) | 7'00" |
| Kết luận | 0'45" |
| Hỏi đáp | 4'00" |

Video demo (4'30", quay sẵn, lồng tiếng):

| Đoạn | Thời lượng | Nội dung |
|---|---|---|
| A. Failover Multi-WAN | 1'00" | `ping -t 8.8.8.8`, ngắt WAN1, chuyển sang WAN2 |
| B. DNSBL | 1'00" | `nslookup`, trang chặn, thử đổi DNS sang 8.8.8.8 |
| C. Suricata IDS | 2'00" | `nmap` rồi `hping3` từ Kali, mở Alerts |
| Chuyển cảnh, chú thích | 0'30" | Chữ tên từng đoạn |

---

## 7. Cắt giảm nếu cần rút ngắn Chương 4

| Thứ tự cắt | Phần | Cách cắt |
|---|---|---|
| 1 | 4.1.3 Công cụ | Gộp vào 4.1.1 |
| 2 | 4.2 | Một đoạn và một hình |
| 3 | 4.4.4 Wireshark | Giữ 1 hình |
| 4 | 4.3.2 Ma trận | Rút còn 4-5 dòng |
| 5 | 4.5.3 Khuyến nghị | Một đoạn dẫn mục 2.1.3 |
| **Không cắt** | 4.3.3, 4.3.4, 4.4.3, 4.4.5, 4.5.2, 4.5.4 | Chứng minh trực tiếp mục tiêu đề tài |

---

## 8. Danh mục kiểm tra trước khi nộp

- [ ] Chương 3 không còn mục "Đánh giá kết quả", không có số đo.
- [ ] Hình 3.5 chụp lại với Legacy Mode, Block Offenders tắt.
- [ ] Đánh số lại hai "Hình 3.6" (3.2.2d và 3.3.2b).
- [ ] Chương 4: "Inline", "IPS" chỉ còn ở Bảng 4.5.2 và mục 4.5.4.
- [ ] Chương 4: không còn "iperf3", "throughput".
- [ ] Chương 4: "QoS", "Traffic Shaper" chỉ còn 1 lần ở mục 4.5.4.
- [ ] Không còn `[...]` trống.
- [ ] Mỗi dòng "Đáp ứng" ở Bảng 4.10 trỏ về một hình hoặc bảng ở 4.3-4.4.
- [ ] Slide so sánh Inline/Legacy ghi rõ "chưa triển khai Inline".
- [ ] Sửa footer slide Chương 2, 3, 4 (ngày "3 November 2024", số trang 9, 32, 32).

---

## 9. Trạng thái

| Việc | Trạng thái |
|---|---|
| Đề cương Chương 3 và 4 | Xong |
| Số đo thực (4.3, 4.4, 4.5) | Chưa |
| 4 slide Chương 4 và video demo | Chưa |
| Rút gọn Chương 1-3 theo ngân sách thời gian | Chưa |
| Tập thuyết trình 2 lượt có bấm giờ | Chưa |
