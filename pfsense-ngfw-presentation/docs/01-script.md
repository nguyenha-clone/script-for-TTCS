# Script thuyết trình (10:30)

Dùng "nhóm em" cho nhóm. Đổi nếu chỉ một người trình bày.

## Khối 1: Mở đầu (0:30), slide 1-2

Kính chào cô Lê Thị Hồng Vân và các bạn. Nhóm em gồm Giang, Hà và Hưởng, xin trình bày đề tài nghiên cứu tường lửa thế hệ mới pfSense và ứng dụng trong doanh nghiệp vừa và nhỏ. Câu hỏi nhóm muốn trả lời là: một giải pháp mã nguồn mở có mang được tính năng của tường lửa thế hệ mới đến doanh nghiệp nhỏ, nơi ngân sách hạn chế, hay không. Bài gồm bốn phần: tổng quan NGFW, pfSense, ứng dụng cho doanh nghiệp, và thực nghiệm, phần này nhóm sẽ trình bày bằng video demo.

## Khối 2: Chương 1 (2:20)

### Slide 4-5 (0:35)
Tường lửa kiểm soát lưu lượng giữa các vùng mạng dựa trên một tập luật. Gói tin được so khớp theo IP nguồn, IP đích, cổng và giao thức: khớp luật thì cho qua, không thì bị loại bỏ. Trong doanh nghiệp, tường lửa còn chia mạng thành ba vùng: Internet là vùng không tin cậy, DMZ chứa web, mail, DNS, và LAN nội bộ chứa hệ thống quan trọng.

### Slide 6-7 (0:30)
Theo mô hình OSI, Packet Filtering và Stateful Inspection làm việc ở tầng 3-4, Circuit-level ở tầng 5, Application Gateway và WAF ở tầng 7. Riêng NGFW trải từ tầng 3 đến 7. Về hình thức, firewall cứng như Cisco ASA, Fortinet là thiết bị chuyên dụng ở biên mạng, còn firewall mềm như iptables chỉ bảo vệ một máy.

### Slide 8-9 (0:45)
Tường lửa phát triển qua ba giai đoạn: Packet Filtering cuối thập niên 80, Stateful Inspection giữa thập niên 90, NGFW giai đoạn 2004-2009. Lý do là tấn công ngày càng tinh vi, điển hình là giấu trong lưu lượng HTTP và HTTPS. Loại lưu lượng này đi qua cổng web quen thuộc nên tường lửa chỉ lọc IP và cổng sẽ cho qua. NGFW bổ sung VPN, kiểm soát ứng dụng, IPS, lọc web, chống mã độc, threat intelligence và phân tích gói tin sâu DPI. Nói ngắn gọn, NGFW hỏi thêm: ứng dụng nào đang chạy và nội dung có an toàn không.

### Slide 10 (0:30)
Doanh nghiệp vừa và nhỏ cần bốn thứ: chống tấn công bằng IPS, kiểm soát ứng dụng, phân quyền theo người dùng, và quản lý tập trung với chi phí vận hành thấp. Đây là thước đo để nhóm đánh giá pfSense ở phần tiếp theo.

## Khối 3: Chương 2 (2:45)

### Slide 12-13 (0:45)
pfSense là nền tảng firewall và router mã nguồn mở trên FreeBSD, quản trị qua WebGUI, bản CE miễn phí, cài được trên máy thường hoặc máy ảo. Kiến trúc có ba nhóm: nền tảng FreeBSD và ZFS; nhóm firewall và network gồm PF, NAT, routing, DNS, VLAN; nhóm security gồm Suricata, pfBlockerNG và hệ sinh thái package. Hai nhóm đầu làm pfSense thành firewall kiêm router. Nhóm thứ ba đưa nó tiến tới NGFW.

### Slide 14-16 (1:10)
Khi thêm package, pfSense vượt qua giới hạn lọc IP và cổng. Nhóm dùng hai package. Thứ nhất là Suricata: phân tích gói tin sâu rồi đi theo hai hướng. Chế độ IDS chỉ phát hiện, ghi log và cảnh báo; chế độ IPS chủ động chặn. Có thể hình dung IDS là camera, IPS là bảo vệ có quyền chặn cửa. Thứ hai là pfBlockerNG, kiểm soát ở ba mặt: danh sách IP theo uy tín, GeoIP theo quốc gia, và DNSBL chặn theo tên miền ở tầng DNS. Suricata soi nội dung gói tin, còn pfBlockerNG chặn từ cửa ngoài dựa trên độ tin cậy của nguồn.

### Slide 17-18 (0:50)
Ba thành phần phối hợp: firewall kiểm soát kết nối, pfBlockerNG lọc IP và domain, Suricata phân tích sâu. Yêu cầu hợp lệ vào được LAN hoặc DMZ, yêu cầu độc hại bị chặn trên đường đi. Ưu điểm: chi phí thấp, linh hoạt phần cứng, nhiều package, quản trị tập trung. Nhược điểm: cần kiến thức kỹ thuật, cấu hình khá phức tạp, hỗ trợ thương mại không bằng giải pháp Enterprise. Tức là đổi chi phí thấp lấy yêu cầu về nhân sự vận hành. Nhóm em xin sang phần ứng dụng.

## Khối 4: Chương 3 (4:15)

### Slide 20 (0:25)
Kiến trúc tổng thể đi từ Internet đến pfSense, qua mô hình Router-on-a-Stick với trunk 802.1Q, rồi đến các VLAN người dùng. Giải pháp có ba khối: định tuyến và phân VLAN, lọc IP và domain bằng pfBlockerNG, và QoS.

### Slide 21 (0:35)
Mô hình 1 chia ba VLAN: VLAN 10 cho nhân viên, VLAN 20 cô lập mạng khách, VLAN 30 cho quản trị và máy chủ Windows Server 2025 với mức bảo mật cao nhất. Ngoài ra có LAN 2 nằm ngoài tường lửa nội bộ, đặt Kali Linux để giả lập tấn công. Toàn bộ dựng trên GNS3.

### Slide 22 (0:25)
Multi-WAN loại bỏ điểm lỗi duy nhất. WAN 1 là đường chính, WAN 2 là dự phòng. pfSense giám sát hai gateway, WAN 1 rớt thì tự chuyển sang WAN 2, ổn định lại thì tự trả về, không cần kỹ thuật viên can thiệp.

### Slide 23-24 (0:50)
Mô hình 2 là phòng thủ đa lớp. Lớp ngoài là pfBlockerNG dùng threat intelligence từ Abuse.ch và Spamhaus DROP, chặn cả hai chiều vào và ra, nên máy nội bộ nhiễm mã độc cũng không liên lạc được với máy chủ điều khiển. Để tránh chặn nhầm, nhóm thêm dải mạng nội bộ vào Pass Lists. Ngoài ra DNSBL chặn tên miền lừa đảo ngay từ bước phân giải DNS.

### Slide 25-26 (0:50)
Lớp trong là Suricata với bộ luật ET Open. Chế độ IDS chỉ cảnh báo. Chế độ IPS có hai kiểu. Inline nằm trực tiếp trên đường đi và drop từng gói theo thời gian thực, nhưng có thể ảnh hưởng băng thông. Legacy chỉ giám sát qua bản sao, ít ảnh hưởng mạng hơn, và chặn bằng cách đưa IP vi phạm vào danh sách chặn. Cần chặn thật thì chọn Inline, ưu tiên không làm chậm mạng thì chọn Legacy.

### Slide 27-28 (0:50)
Mô hình 3 có hai phần. Quản lý truy cập: bật Unbound DNS, tạo nhóm BLOCK_SOCIALNETWORK chứa Facebook, TikTok, Instagram, Telegram trong DNSBL, điều hướng tên miền bị chặn về IP ảo 10.10.10.1, và chặn port 853 để chống lách bằng DoT, ép máy trạm dùng DNS của pfSense. Tối ưu băng thông: QoS chia ba hàng đợi, 40% cho VoIP và real-time, 50% cho web và email, dưới 10% cho P2P và torrent. Giờ mời cô và các bạn xem kết quả thực nghiệm qua video.

## Video demo (4:30)

Xem [02-demo-narration.md](02-demo-narration.md).

## Khối 5: Kết luận (0:40), đọc sau video, trước hỏi đáp

Tóm lại, với pfSense cùng Suricata và pfBlockerNG, doanh nghiệp vừa và nhỏ có thể xây dựng hệ thống đáp ứng các yêu cầu cốt lõi của NGFW: chống tấn công, kiểm soát truy cập, quản lý tập trung, với chi phí thấp. Đổi lại, cần nhân sự đủ năng lực vận hành. Nhóm em xin cảm ơn cô và các bạn, và sẵn sàng trả lời câu hỏi.
