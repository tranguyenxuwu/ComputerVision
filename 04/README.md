# Lab 04: Geometric Transforms & Image Filtering

Bài thực hành này tập trung vào các kỹ thuật biến đổi hình học (Geometric Transforms) và lọc ảnh (Image Filtering) sử dụng hai thư viện phổ biến là **PIL** và **OpenCV**.


## 1. Geometric Transforms với PIL
Notebook: `2.4.1_Gemetric_trasfroms_PIL.ipynb`

Trong phần này, chúng ta sử dụng thư viện **PIL** để thực hiện các biến đổi hình học. Các thao tác được thực hiện theo hướng đối tượng, tác động trực tiếp lên các đối tượng `Image`.

### Các kỹ thuật chính:
- **Scaling**: Sử dụng phương thức `.resize((width, height))` để thay đổi kích thước ảnh.
- **Cropping**: Sử dụng phương thức `.crop((left, top, right, bottom))` với tham số là một tuple định nghĩa toạ độ 4 góc.
- **Translation**: Trong PIL, việc dịch chuyển thường được thực hiện thông qua `ImageChops.offset()` hoặc dán ảnh lên một nền lớn hơn tại toạ độ mong muốn.
- **Rotation**: Sử dụng phương thức `.rotate(angle)` để xoay ảnh ngược chiều kim đồng hồ.
- **Flipping**: Sử dụng module `ImageOps` với các hàm `ImageOps.flip()` (lật dọc) và `ImageOps.mirror()` (lật ngang).

## 2. Geometric Transforms với OpenCV
Notebook: `2.4.2_Gemetric_trasfroms_OpenCV.ipynb`

Phần này thực hiện các biến đổi tương tự nhưng sử dụng **OpenCV**. Điểm khác biệt lớn nhất là OpenCV xử lý ảnh dưới dạng các **ma trận NumPy**. Việc biến đổi hình học thường dựa trên các ma trận biến đổi.

### Các kỹ thuật chính:
- **Scaling**: Sử dụng hàm `cv2.resize(src, dsize, fx, fy)`. Có thể chỉ định kích thước đích hoặc tỉ lệ scale theo trục x, y.
- **Translation**: Cần tạo một ma trận dịch chuyển $M$ kích thước 2x3:
  $$M = \begin{bmatrix} 1 & 0 & t_x \\ 0 & 1 & t_y \end{bmatrix}$$
  Sau đó áp dụng hàm `cv2.warpAffine()`.
- **Rotation**: Để xoay ảnh, cần tạo ma trận xoay bằng hàm `cv2.getRotationMatrix2D(center, angle, scale)`. Sau đó cũng sử dụng `cv2.warpAffine()` để thực hiện biến đổi.
- **Cropping**: Thực hiện đơn giản bằng cách cắt mảng NumPy: `cropped_img = img[y:y+h, x:x+w]`.
- **Flipping**: Sử dụng hàm `cv2.flip(src, flipCode)` (0: trục x, 1: trục y, -1: cả hai trục).

## 3. Image Filtering & Processing
Notebook: `2374802010352_TranNhatNguyen_Lab02.ipynb`

Notebook này giải quyết các bài tập về các kỹ thuật lọc và xử lý ảnh từ cơ bản đến nâng cao:

### Các kỹ thuật thực hiện:
- **Lọc ảnh**:
  - **Box Filter**: Làm mờ ảnh, kiểm chứng với các kích thước kernel khác nhau.
  - **Gaussian Filter**: Làm mờ ảnh, sử dụng kernel lẻ.
  - **Median Filter**: Lọc trung vị, đặc biệt hiệu quả với nhiễu muối tiêu.
- **So sánh & Khử nhiễu**: Thực nghiệm trên ảnh `pepper_noise` để thấy sự vượt trội của Median Filter so với Gaussian Filter đối với nhiễu dạng hạt.
- **Cân bằng Histogram**: Tăng độ tương phản cho ảnh sau khi đã chuyển sang ảnh xám.
- **Tách đối tượng**: Sử dụng phép trừ ảnh (`absdiff`), nghịch đảo (`bitwise_not`) và các phép toán hình thái học (Morphological Operations) để tách quả bóng ra khỏi nền.
- **Phát hiện biên & đường thẳng**:
  - Áp dụng **Sobel** và **Canny** detector để tìm biên.
  - Sử dụng **Hough Transform** để phát hiện đường thẳng.

## 4. So sánh PIL và OpenCV

- **PIL** thích hợp cho các tác vụ xử lý ảnh đơn giản, cắt ghép, thay đổi kích thước nhanh chóng mà không cần kiến thức sâu về đại số tuyến tính.
- **OpenCV** là lựa chọn hàng đầu cho các bài toán Thị giác máy tính (Computer Vision) phức tạp, yêu cầu tốc độ xử lý cao và khả năng tùy biến sâu thông qua các ma trận biến đổi.
