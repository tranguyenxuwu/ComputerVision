## BÀI THỰC HÀNH TUẦN 2

### 1. Quy trình xử lý trong file PIL (`2.2.1_basic_image_manipulation_PIL.ipynb`)

Quy trình trong file này đi theo hướng **tác động lên đối tượng ảnh** và sử dụng các module hỗ trợ chuyên biệt:

* **Quản lý bộ nhớ & Sao chép:**
* Nhấn mạnh việc sử dụng phương thức `copy()` để tạo ra một đối tượng ảnh mới độc lập.
* Nếu chỉ gán biến (`B = A`), PIL chỉ tạo tham chiếu; thay đổi trên B sẽ ảnh hưởng A (Aliasing).
* **Thao tác hình học :**
* **Lật ảnh:** Không gọi trực tiếp từ đối tượng ảnh mà thông qua module tiện ích `ImageOps` (ví dụ: `ImageOps.flip`, `ImageOps.mirror`).
* **Cắt ảnh:** Sử dụng phương thức `crop()` với tham số là một tuple toạ độ hình hộp `(left, upper, right, lower)`.
* **Thao tác vẽ & Chồng lớp:**
* **Vẽ:** Phải khởi tạo một đối tượng "họa sĩ" riêng từ lớp `ImageDraw` (`draw = ImageDraw.Draw(image)`). Các nét vẽ (hình chữ nhật, văn bản) được thực hiện thông qua đối tượng `draw` này.
* **Chồng ảnh:** Sử dụng hàm `paste()` để dán một đối tượng ảnh này lên đối tượng ảnh khác.

### 2. Quy trình xử lý trong file OpenCV (`2.2.2_basic_image_manipulation_open_CV.ipynb`)

Quy trình trong file này đi theo hướng **tính toán và biến đổi trực tiếp trên mảng số học (Numpy Array)**:

* **Quản lý bộ nhớ & Sao chép:**
* Ảnh là một mảng Numpy. Việc gán biến chỉ là trỏ đến cùng vùng nhớ.
* Sử dụng phương thức `image.copy()` để sao chép dữ liệu mảng sang vùng nhớ mới, tránh làm hỏng ảnh gốc khi xử lý.
* **Thao tác hình học:**
* **Lật ảnh:** Sử dụng hàm `cv2.flip()` với tham số mã hóa hướng lật (0: trục x, 1: trục y, -1: cả hai).
* **Cắt ảnh:** Không dùng hàm cắt chuyên biệt mà sử dụng kỹ thuật **Array Slicing** (cắt mảng) của Python: `image[start_row:end_row, start_col:end_col]`.
* **Thao tác vẽ & Pixel:**
* **Vẽ:** Gọi trực tiếp các hàm của thư viện `cv2` (như `cv2.rectangle`, `cv2.putText`) tác động thẳng lên mảng ảnh gốc (In-place operation). Không cần tạo đối tượng trung gian.
* **Thao tác Pixel:** Truy cập và thay đổi giá trị pixel trực tiếp qua chỉ số mảng: `image[row, col]`.

### SO SÁNH

#### 1. Cắt ảnh

* **PIL:** Tư duy theo **Toạ độ Descartes**. Cần xác định góc trên-trái và góc dưới-phải `(left, top, right, bottom)`.
* **OpenCV:** Tư duy theo **Chỉ số Ma trận**. Cắt theo thứ tự Hàng (Cao) trước, Cột (Rộng) sau `[y:y+h, x:x+w]`.

#### 2. Lật ảnh

* **PIL:** Dựa vào module ngoại vi `ImageOps`.
* **OpenCV:** Dựa vào tham số số học (projection flag) truyền vào hàm `flip`.

#### 3. Vẽ lên ảnh

* **PIL:** Cần tạo một layer (lớp) vẽ riêng biệt (`ImageDraw`).
* **OpenCV:** "Vẽ" thực chất là thay đổi giá trị các phần tử trong ma trận ảnh gốc. Hành động này có tính phá hủy (destructive) nếu không sao chép ảnh trước.

#### 4. Hiển Thị

* **PIL:** Tương thích tốt với việc hiển thị trực tiếp trong Jupyter Notebook.
* **OpenCV:** Vẫn phải phụ thuộc vào `matplotlib` và cần chuyển đổi không gian màu (BGR -> RGB) để hiển thị đúng màu sắc mắt người nhìn thấy.