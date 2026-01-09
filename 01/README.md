### SO SÁNH SỰ KHÁC BIỆT CỦA PIL VÀ OPENCV

#### 1. Cách Đọc và Kiểu Dữ Liệu 

* **PIL (File 2.1.1):**
* Sử dụng hàm `Image.open()`.
* Kết quả trả về là một **Object** của lớp `PIL`. 

* **OpenCV (File 2.1.2):**
* Sử dụng hàm `cv2.imread()`.
* Kết quả trả về là một **mảng Numpy (numpy array)** chứa các giá trị số nguyên 8-bit (uint8).

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
