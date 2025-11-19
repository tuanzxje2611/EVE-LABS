# 🌐 Triển khai MPLS L3 VPN (Dual Customer)

Dự án này triển khai một giải pháp mạng **MPLS Layer 3 Virtual Private Network (L3 VPN)**, cho phép nhà cung cấp dịch vụ (Service Provider - SP) cung cấp dịch vụ mạng riêng biệt và bảo mật cho nhiều khách hàng (Customer A và Customer B) thông qua cùng một cơ sở hạ tầng trục mạng (MPLS Core).

## 🚀 Cấu trúc Mạng

Mạng được chia thành 3 phần chính:

1.  **MPLS Core (Provider/P & Provider Edge/PE):** Gồm các Router R2, R3, R4, R5.
2.  **Customer A:** Gồm các Router R1A và R6A.
3.  **Customer B:** Gồm các Router R1B và R6B.
  <img width="1332" height="547" alt="Screenshot 2025-11-19 225501" src="https://github.com/user-attachments/assets/1bc48f12-35b6-4906-9c7d-61e925a9fff9" />

## 🛠️ Chi tiết Cấu hình Khách hàng

### 1. Khách hàng A (Customer A) - Sử dụng Static Routing

| Đặc điểm | Mô tả | Chi tiết cấu hình |
| :--- | :--- | :--- |
| **RD (Route Distinguisher)** | Phân biệt các prefix của khách hàng trong BGP (VPNv4) | `16:16` trên R2 và R5 |
| **RT Export/Import (R2)** | Xác định prefix nào được gửi đi/nhận về | Export `1:6`, Import `6:1` |
| **RT Export/Import (R5)** | Xác định prefix nào được gửi đi/nhận về | Export `6:1`, Import `1:6` |
| **Giao thức CE-PE** | Static Routing | Prefix **1.1.1.0/24** (R1A) và **6.6.6.0/24** (R6A) được định tuyến tĩnh và **Redistribute Static** vào BGP VRF |

* **Lưu ý:** Cấu hình RT Export/Import được thiết lập đối xứng và đảo ngược giữa R2 và R5 (`1:6` & `6:1`) để đảm bảo các prefix có thể được trao đổi thành công.

### 2. Khách hàng B (Customer B) - Sử dụng eBGP

| Đặc điểm | Mô tả | Chi tiết cấu hình |
| :--- | :--- | :--- |
| **RD (Route Distinguisher)** | Phân biệt các prefix của khách hàng trong BGP (VPNv4) | `11:66` trên R2 và R5 |
| **RT Export/Import** | Sử dụng cấu hình đồng nhất | `route-target both 11:66` |
| **Giao thức CE-PE** | eBGP | R1B (AS 1) và R6B (AS 6) thiết lập eBGP neighbor với R2 và R5 (AS 2345) |

---
## 📂 Các File Cấu hình

Dự án này bao gồm các file cấu hình chi tiết cho từng thiết bị:

| Tên File | Thiết bị | Vai trò |
| :--- | :--- | :--- |
| `R1A.txt`, `R6A.txt` | CE A | Router khách hàng A (Static) |
| `R1B.txt`, `R6B.txt` | CE B | Router khách hàng B (eBGP) |
| `R2.txt`, `R5.txt` | PE | Router biên nhà cung cấp dịch vụ (MPLS, OSPF, BGP, VRF) |
| `R3.txt`, `R4.txt` | P | Router trục nhà cung cấp dịch vụ (MPLS, OSPF) |
