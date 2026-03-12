# Lab 05: Xử lý ảnh trong miền tần số (Frequency Domain)

Bài thực hành này tập trung vào kỹ thuật chuyển đổi cấu trúc ảnh từ miền không gian (spatial domain) sang miền tần số (frequency domain) sử dụng Biến đổi Fourier, cụ thể là **Fast Fourier Transform (FFT)** và **Discrete Fourier Transform (DFT)**.

## 1. Biến đổi Fourier với OpenCV (DFT)
Notebook: `thuchanh_fft.ipynb`

Phần này sử dụng thư viện **OpenCV** (`cv2.dft`) và NumPy để thực hiện Biến đổi Fourier Rời rạc (DFT) trên hình ảnh, cho phép phân tích các sóng tần số cấu thành nên một bức ảnh.

### Các thao tác và kỹ thuật chính:
- **Chuẩn bị dữ liệu**: Đọc ảnh dưới dạng ảnh xám (grayscale) và chuyển đổi mảng ảnh numpy sang định dạng dấu phẩy động `np.float32`.
- **Thực hiện DFT**: Sử dụng hàm `cv2.dft(..., flags=cv2.DFT_COMPLEX_OUTPUT)` để biến đổi và nhận về ma trận số phức (bao gồm phần thực và phần ảo đại diện cho biên độ và pha của sóng).
- **Dịch tâm phổ (FFT Shift)**: Mặc định sóng tần số 0 (DC component - phần nền sáng nhất) nằm ở góc phần tư trên cùng bên trái. Kỹ thuật `np.fft.fftshift()` được dùng để dịch chuyển gốc tần số thấp ra giữa trung tâm biểu đồ.
- **Tính toán phổ biên độ (Magnitude Spectrum)**: 
  - Tính độ lớn của vector số phức bằng `cv2.magnitude()`.
  - Đưa về **thang đo logarit** (log scale): Do điểm tần số ở tâm quá lớn so với các tần số xung quanh, nếu vẽ trực tiếp sẽ chỉ thấy một điểm sáng lóe lên ở giữa đè bẹp các chi tiết khác. Việc sử dụng hàm log (`20 * np.log(...)`) làm mượt đi sự chênh lệch lớn này, giúp hiện rõ các luồng sáng tần số cao bao quanh.

## 2. Fast Fourier Transform (FFT) phát hiện ảnh mờ
Notebook: `thuchanh_fft.ipynb`

Phần này nâng cao hơn khi kết hợp thuật toán biến đổi nhanh FFT của thư viện **SciPy** (`scipy.fft`) và các bộ lọc của **Scikit-Image** (`skimage`) để đi sâu vào phân tích sự thay đổi phổ tần số đặc trưng khi ảnh bị làm mờ.

### Các thao tác và kỹ thuật chính:
- **Lọc ảnh (Filtering)**: Áp dụng hàm `difference_of_gaussians` làm mờ ảnh để tạo ra một biểu đồ ảnh thứ hai so sánh với biểu đồ ảnh gốc.
- **Áp dụng cửa sổ (Windowing)**: Nhân cả ảnh mờ và ảnh gốc với một hàm cửa sổ tĩnh (`window('hann')`). Bước này giúp giảm thiểu hiện tượng "rò rỉ phổ" (Spectral Leakage) sinh ra do các tín hiệu nhiễu lặp lại viền cạnh ở hình ảnh nguồn hữu hạn, đảm bảo FFT tính toán ra kết quả trơn tru hơn.
- **Tính toán FFT Dimension**: Hàm `fftn()` được sử dụng thay vì `fft` thông thường do hình ảnh bản chất là ma trận dữ liệu trên nhiều chiều (2D array). Sau đó, `fftshift` lại tiếp tục được dùng để dời tần số trung tâm vào giữa.
- **Đánh giá mức độ bảo lưu chi tiết**: Phân tích phổ biên độ của ảnh lọc và phổ biên độ ảnh gốc. Bằng quan sát trực quan đồ thị màu magma, có thể thấy tần số cao (màu sáng chạy dạt ra rìa biểu đồ) ở ảnh bị làm mờ đã biến mất đáng kể vì các đường biên gắt (cạnh) đã mịn đi. Rút ra kết luận thuật toán FFT Magnitude log-scale có độ nhạy rất tốt trong việc phát hiện sự thay đổi độ nét (blur detection).
