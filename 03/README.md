## BÀI THỰC HÀNH TUẦN 3

### 1. Quy trình xử lý cơ bản với OpenCV (`Opencv_Basics.ipynb`)

Quy trình trong file giúp làm quen với các thao tác nền tảng của thư viện OpenCV:

* **Đọc và Hiển thị ảnh:**
  * OpenCV mặc định đọc ảnh theo chuẩn **BGR**, trong khi Matplotlib hiển thị theo chuẩn **RGB**.
  * Giải pháp: Cần chuyển đổi hệ màu để hiển thị đúng.
    * Cách 1: Slicing mảng `image[:, :, ::-1]`.
    * Cách 2: Sử dụng hàm convert `cv2.cvtColor(image, cv2.COLOR_BGR2RGB)`.
* **Biến đổi hình học cơ bản:**
  * **Phóng to/Thu nhỏ:** Sử dụng `cv2.resize()`. Cần lưu ý chọn phương pháp nội suy phù hợp (ví dụ `cv2.INTER_LINEAR` cho mặc định, `cv2.INTER_CUBIC` cho chất lượng tốt hơn).
  * **Cắt ảnh (Crop):** Sử dụng kỹ thuật **Array Slicing** trên mảng Numpy: `image[start_y:end_y, start_x:end_x]`.
  * **Ghép ảnh:** Sử dụng `cv2.vconcat` (dọc) và `cv2.hconcat` (ngang) hoặc các hàm stack của Numpy.

### 2. Quy trình xử lý Histogram và Biến đổi cường độ (`2.3.2_ Histograms and Intensity Transformations.ipynb`)

Quy trình trong file này giúp phân tích và điều chỉnh giá trị pixel (Intensity) để cải thiện chất lượng ảnh:

* **Phân tích Histogram:**
  * Sử dụng biểu đồ Histogram để quan sát sự phân bố tần suất của các giá trị mức xám (intensity levels) trong ảnh.
  * Ảnh tối sẽ có histogram lệch trái, ảnh sáng lệch phải.
* **Biến đổi cấp độ xám (Intensity Transformations):**
  * **Image Negatives:** `s = L - 1 - r`. Đảo ngược màu (giống phim âm bản), hữu ích để làm nổi bật chi tiết trắng trên nền đen.
  * **Log Transformations:** `s = c * log(1 + r)`. Dùng để mở rộng các giá trị pixel tối trong ảnh (nén dải động), giúp nhìn rõ chi tiết ở vùng tối.
  * **Power-Law (Gamma) Transformations:** `s = c * r^gamma`. Linh hoạt hơn Log, có thể làm sáng (`gamma < 1`) hoặc làm tối (`gamma > 1`) ảnh.
* **Cân bằng Histogram (Histogram Equalization):**
  * Kỹ thuật tự động điều chỉnh độ tương phản bằng cách kéo giãn phân bố độ sáng ra toàn dải. Giúp ảnh có độ tương phản tốt hơn, chi tiết rõ ràng hơn.

### 3. Quy trình xử lý dữ liệu bảng (`Consumption.ipynb`)

Quy trình này khác biệt hoàn toàn so với hai file trên, tập trung vào **xử lý dữ liệu dạng bảng (Tabular Data)** thay vì ma trận ảnh:

* **Sử dụng Pandas:**
  * Đọc dữ liệu từ file CSV: `pd.read_csv()`.
  * Kiểm tra cấu trúc dữ liệu: `head()`, `tail()`, `info()`.
* **Xử lý chuỗi thời gian:**
  * Chuyển đổi cột dữ liệu sang định dạng thời gian chuẩn: `pd.to_datetime()`.
  * Điều này cho phép thực hiện các phép toán hoặc truy vấn dựa trên ngày tháng dễ dàng hơn.