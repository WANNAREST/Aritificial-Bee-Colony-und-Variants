# Thuật Toán Bầy Ong Nhân Tạo (Artificial Bee Colony), Các Biến Thể và ỨNG DỤNG TRONG BÀI TOÁN HỘI TỤ CHÙM TIA

 **Báo cáo code cho Project Kỹ Thuật Truyền Thông**

> ⚠️ **LƯU Ý QUAN TRỌNG:** Các file `.ipynb` trong repository này chứa nhiều phương trình toán học, biểu đồ và visualization phức tạp. Trong trường hợp không thể xem trên github được, **bạn nên tải các file về máy và mở bằng Jupyter Notebook/Google Colab để xem đầy đủ nội dung**. 

---

## 📖 Tổng Quan

Repository này chứa implementation của thuật toán **Artificial Bee Colony (ABC)** và các biến thể cải tiến của nó, áp dụng vào bài toán tối ưu hóa trong truyền thông không dây, cụ thể là:

- **Bài toán:** Hybrid Beamforming cho hệ thống MIMO lớn (Extremely Large Antenna Arrays - ELAA) trong môi trường trường gần (Near-field) ở băng tần Terahertz
- **Mục tiêu:** Tối ưu hóa Sum-Rate (tổng tốc độ truyền) cho multi-user downlink
- **Kỹ thuật:** Two-stage optimization với ABC/GABC/IABC tối ưu Analog Beamforming và Zero-Forcing cho Digital Beamforming

---

## 📁 Cấu Trúc Repository

### 1. `ABC_into_paper_(5).ipynb`
**File chính - Áp dụng ABC vào bài toán Hybrid Beamforming cho hệ thống 6G**

#### Nội dung bao gồm: 

**Module 1: Cấu hình Hệ thống & Mô hình Kênh Trường Gần**
- Thiết lập tham số hệ thống (64 ăng-ten, 4 chuỗi RF, 16 subcarriers, 100 GHz, 10 GHz bandwidth)
- Mô hình kênh truyền Near-field với: 
  - Hiệu ứng băng rộng (Wideband Effect): Tần số thay đổi theo subcarrier
  - Mặt sóng cầu (Spherical Wavefront): Khoảng cách phi tuyến giữa các ăng-ten
  - Công thức:  `r_{n,k} = sqrt(r_k^2 + delta_n^2*d^2 - 2*r_k*delta_n*d*cos(theta_k))`

**Module 2: Hàm Mục Tiêu (Objective Function)**
- Chiến lược Two-Stage Optimization:
  - **Stage 1:** ABC tối ưu biến Analog (Phase Shifters + TTD)
  - **Stage 2:** Zero-Forcing tính trực tiếp biến Digital
- Xử lý: 
  - Tái tạo ma trận Analog với Unit-Modulus Constraint
  - Tính ma trận TTD phụ thuộc tần số
  - Tính kênh hiệu dụng và Digital Beamformer
  - Chuẩn hóa công suất
  - Tính SINR và Sum-Rate

**Module 3: Thuật toán ABC Tiêu chuẩn**
- Tham số: 40 ong, 100 vòng lặp, limit=10
- Ba giai đoạn:
  1. **Ong Thợ (Employed Bees):** Khám phá lân cận
  2. **Ong Quan Sát (Onlooker Bees):** Khai thác vùng tốt (Roulette Wheel)
  3. **Ong Trinh Sát (Scout Bees):** Reset nguồn thức ăn cạn kiệt
- Công thức cập nhật:  `v_ij = x_ij + phi*(x_ij - x_kj)`

**Biến thể 1: GABC (Gbest-guided ABC)**
- Cải tiến công thức tìm kiếm với lực hút về Global Best: 
  - `v_ij = x_ij + phi*(x_ij - x_kj) + psi*(gbest_j - x_ij)`
  - Tham số C=1.5 (hệ số gia tốc)
- Tăng tốc độ hội tụ so với ABC chuẩn

**Biến thể 2: IABC (Improved ABC)**
- Bước nhảy thích nghi (Adaptive Step Size):
  - Tính góc giữa hướng di chuyển và hướng tới Best
  - Điều chỉnh SF (Scaling Factor) dựa trên cos(phi)
- Xác suất chuyển tiếp thích nghi:
  - P1 (khám phá) và P2 (khai thác) trộn theo thời gian
  - Multi-group search với sub-colonies

---

### 2. `ABC_và_các_biến_thể. ipynb`
**File demo - Minh họa thuật toán ABC trên hàm test benchmark**

#### Nội dung: 

**Thuật toán ABC tiêu chuẩn**
- Hàm test:  Schaffer function (hàm có nhiều cực tiểu địa phương)
- Cấu trúc đầy đủ 3 giai đoạn (Employed/Onlooker/Scout Bees)
- Visualization:  Biểu đồ hội tụ qua các vòng lặp

**Biến thể GABC**
- So sánh trực quan với ABC chuẩn
- Chạy song song 2 thuật toán và vẽ biểu đồ so sánh
- Trục Y logarit để thấy rõ tốc độ hội tụ

**Biến thể IABC**
- Triển khai đầy đủ: 
  - Adaptive SF với cosine angle
  - Multi-group search strategy
  - Dynamic probability switching
- Tham số: SF_INIT=0.5, BETA=10. 0, SUB_COLONY_RATIO=0.5

---

## 🚀 Cách Sử Dụng

### Yêu cầu
```bash
pip install numpy matplotlib jupyter
```

### Chạy Notebook

**Cách 1: Local**
```bash
jupyter notebook ABC_into_paper_\(5\).ipynb
```

**Cách 2: Google Colab**
1. Upload file `.ipynb` lên Google Drive
2. Mở bằng Google Colab
3. Run All Cells

### Tùy chỉnh Tham số

**Trong `ABC_into_paper_(5).ipynb`:**
```python
class Config:
    N = 64          # Số ăng-ten (thay đổi:  32, 128, 256)
    N_RF = 4        # Số RF chains (thay đổi: 2, 8, 16)
    K = 4           # Số người dùng (thay đổi: 2, 8, 16)
    M = 16          # Số subcarriers (thay đổi: 8, 32, 64)
    maxCycle = 100  # Số vòng lặp ABC (tăng lên 200-500 cho kết quả tốt hơn)
```

**Trong `ABC_và_các_biến_thể.ipynb`:**
```python
SN = 40           # Tổng số ong (thay đổi: 20, 60, 100)
maxCycle = 2000   # Số vòng lặp (thay đổi: 500, 5000)
D = 3             # Số chiều bài toán (thay đổi: 2, 5, 10)
C = 1.5           # Tham số GABC (thay đổi: 1.0, 2.0)
```

---

## 📊 Kết Quả Mong Đợi

### ABC vào Hybrid Beamforming
- **Sum Rate:** ~140-142 bits/s/Hz (phụ thuộc vào channel realization)
- **Thời gian:** ~2-3 phút (100 vòng lặp, 40 ong)
- **Biểu đồ:** Đường hội tụ tăng dần, có thể có plateaus (vùng phẳng)

### So sánh ABC vs GABC vs IABC
- **ABC:** Hội tụ chậm nhưng ổn định
- **GABC:** Hội tụ nhanh hơn 20-30%, có thể hội tụ sớm
- **IABC:** Cân bằng giữa khám phá và khai thác, tốt với bài toán nhiều chiều

---

## 🔬 Chi Tiết Kỹ Thuật

### Mô hình Hệ thống
- **Kiến trúc:** Sub-connected Hybrid Beamforming với TTD
- **Ràng buộc:**
  - Phase Shifters: Unit modulus `|A(i,j)| = 1`
  - TTD: `0 <= t <= t_max`
  - Power:  `||A*T*D||_F^2 <= P_t`

### Độ phức tạp
- **Không gian tìm kiếm:** D = N + N_RF (68 chiều với N=64, N_RF=4)
- **Số đánh giá hàm mục tiêu:** SN/2 * maxCycle * 2 (Employed + Onlooker)
- **Ví dụ:** 20 * 100 * 2 = 4000 evaluations

---

## 📖 Tài Liệu Tham Khảo

### Thuật toán ABC
- D. Karaboga (2005). "An Idea Based on Honey Bee Swarm for Numerical Optimization"
- Zhu & Kwong (2010). "Gbest-guided artificial bee colony algorithm"

### Hybrid Beamforming
- Paper gốc: "Beamfocusing Optimization for Near-Field Wideband Multi-User Communications"
---



## 🤝 Đóng Góp

Đây là project học thuật.  Nếu bạn muốn cải thiện: 
1. Fork repository
2. Tạo branch mới (`git checkout -b feature/improvement`)
3. Commit changes (`git commit -m 'Add improvement'`)
4. Push (`git push origin feature/improvement`)
5. Tạo Pull Request

---

## 📜 License

Project thuộc về mục đích học tập và nghiên cứu. Vui lòng trích dẫn nguồn khi sử dụng. 

---

## 👤 Tác Giả

**WANNAREST**  
📧 Contact: [GitHub Profile](https://github.com/WANNAREST)

---

## 🌟 Acknowledgments

- Cảm ơn thầy Trịnh Văn Chiến, anh Nguyễn Quang Đông, anh Vũ Minh Quân đã hướng dẫn bọn em hoàn thiện bài tập lớn này
- Cảm ơn các tác giả paper gốc về Hybrid Beamforming
