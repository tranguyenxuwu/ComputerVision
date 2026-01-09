### 1. Quy trình xử lý trong file PIL (`2.1.1_Images_with_python_library_PIL.ipynb`)

Quy trình trong file này đi theo hướng **tác động lên đối tượng ảnh**:

* **Khởi tạo & Nạp dữ liệu:**
* Bắt đầu bằng việc nhập khẩu lớp `Image` từ thư viện `PIL`.

* **Khảo sát thuộc tính:**
* Truy xuất trực tiếp các thuộc tính của đối tượng như `width`, `height`, `format`, và `mode`.

* **Thao tác hình học:**
* Thực hiện cắt ảnh (`crop`) bằng cách định nghĩa tọa độ hộp (box).
* Xoay ảnh (`rotate`) theo góc độ tùy ý.
* Dán ảnh này lên ảnh khác (`paste`) hoặc ghép nối các ảnh lại với nhau.

* **Thao tác màu sắc & Pixel:**
* Tách các kênh màu **RGB** thành các ảnh riêng biệt.
* Chuyển đổi ảnh sang ảnh đen trắng (Grayscale) hoặc thay đổi độ sâu màu (Quantization).
* Vẽ thêm các hình khối hoặc văn bản trực tiếp lên đối tượng ảnh bằng module `ImageDraw`.
* **Kết xuất:**
* Lưu ảnh đã xử lý ra file mới (`save`) hoặc hiển thị trực tiếp trên notebook.

### 2. Quy trình xử lý trong file OpenCV (`2.1.2_Images_with_python_library_CV.ipynb`)

Quy trình trong file này đi theo hướng **tính toán trên ma trận số**:

* **Khởi tạo & Nạp dữ liệu:**
* Bắt đầu bằng việc nhập khẩu thư viện `cv2`.
* Đọc ảnh từ đường dẫn. Dữ liệu ảnh ngay lập tức được nạp vào bộ nhớ dưới dạng một mảng đa chiều (Numpy array).

* **Chuẩn bị hiển thị:**
* Do đặc thù của OpenCV đọc theo chuẩn BGR, file thực hiện bước chuyển đổi không gian màu sang RGB (`cv2.cvtColor`) để hiển thị đúng màu sắc khi sử dụng thư viện vẽ biểu đồ `matplotlib`.

* **Thao tác trên mảng:**
* Kiểm tra kích thước ảnh thông qua thuộc tính `shape` của mảng (chiều cao, chiều rộng, số kênh).
* Cắt ảnh bằng kỹ thuật cắt **array slicing** dựa trên chỉ số hàng và cột.
* Truy cập và thay đổi giá trị của từng kênh màu riêng biệt thông qua chỉ số mảng.

* **Các phép biến đổi:**
* Thay đổi kích thước ảnh (`resize`) theo tỷ lệ hoặc kích thước cố định.
* Xoay ảnh bằng cách tạo một ma trận xoay (`getRotationMatrix2D`) và áp dụng biến đổi affine (`warpAffine`).

* **Phân tích:**
* Vẽ biểu đồ Histogram để phân tích sự phân bố cường độ sáng của các kênh màu (Red, Green, Blue) trong ảnh.

### SO SÁNH SỰ KHÁC BIỆT CỦA PIL VÀ OPENCV

#### 1. Cách Đọc và Kiểu Dữ Liệu 

* **PIL (File 2.1.1):**
* Sử dụng hàm `Image.open()`.
* Kết quả trả về là một **Object** của lớp `PIL`. 

* **OpenCV (File 2.1.2):**
* Sử dụng hàm `cv2.imread()`.
* Kết quả trả về là một **numpy array** chứa các giá trị số nguyên 8-bit.

#### 2. Không Gian Màu 

* **PIL:** Mặc định đọc ảnh theo thứ tự kênh màu **R-G-B**
* **OpenCV:** Mặc định đọc ảnh theo thứ tự **B-G-R**

#### 3. Cách Hiển Thị Ảnh 

* **PIL:** Gọi tên biến chứa ảnh, ảnh sẽ tự động hiển thị.
* **OpenCV:**
* Hàm `cv2.imshow()` thường gây lỗi hoặc làm treo Jupyter Notebook (như trong file 2 đã chú thích comment lại đoạn mã này). Thay vào đó, file 2 sử dụng `matplotlib.pyplot` để hiển thị: `plt.imshow(image)`.Do sự khác biệt RGB/BGR nói trên, ảnh hiển thị sẽ bị sai màu nếu không dùng `cv2.cvtColor(image, cv2.COLOR_BGR2RGB)` trước khi hiển thị.



#### 4. Thao Tác XLA

* **PIL (Hướng đối tượng):** Các thao tác xử lý thường là các phương thức gắn liền với đối tượng ảnh.
* **OpenCV (Xử lý ma trận):** Các thao tác xử lý thực chất là thao tác trên mảng số học.
