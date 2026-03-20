# Lab 05: Phân đoạn ảnh 
Bài thực hành này tập trung vào các kỹ thuật phân đoạn ảnh — chia ảnh thành các vùng có ý nghĩa dựa trên cường độ, màu sắc, kết cấu hoặc biên cạnh, sử dụng OpenCV, NumPy và Scikit-learn.

Script: `LAB05_ImageSegmentation.ipynb`

## 1. Thresholding để phân đoạn ảnh
Phương pháp đơn giản nhất: chọn ngưỡng cường độ để phân chia pixel thành foreground và background.

### Các kỹ thuật chính:
- **Binary Thresholding**: Sử dụng `cv2.threshold()` với các ngưỡng cố định (80, 127, 180) để so sánh ảnh hưởng của giá trị ngưỡng đến kết quả phân đoạn.
- **Binary Inverse**: Đảo ngược kết quả binary — pixel trên ngưỡng thành đen, dưới ngưỡng thành trắng.
- **Adaptive Thresholding**: Sử dụng `cv2.adaptiveThreshold()` với hai phương pháp Mean-C và Gaussian-C — tính ngưỡng cục bộ cho từng vùng nhỏ, phù hợp ảnh có chiếu sáng không đều.

## 2. Otsu Algorithm để phân đoạn ảnh
Thuật toán Otsu tự động tìm ngưỡng tối ưu bằng cách tối thiểu hóa phương sai nội lớp, đặc biệt hiệu quả với ảnh có hai đỉnh.

### Các kỹ thuật chính:
- **Otsu Thresholding**: Sử dụng `cv2.threshold()` với cờ `cv2.THRESH_OTSU` — OpenCV tự động tính ngưỡng tối ưu từ histogram của ảnh.
- **Gaussian Blur + Otsu**: Kết hợp `cv2.GaussianBlur()` trước khi áp dụng Otsu để làm mượt histogram, giảm nhiễu và cải thiện kết quả phân đoạn.
- **Phân tích Histogram**: Hiển thị histogram với đường ngưỡng Otsu để trực quan hóa vị trí phân tách tối ưu giữa hai lớp pixel.

## 3. K-Means Clustering trong phân đoạn ảnh
Phân đoạn ảnh dựa trên màu sắc bằng cách gom nhóm các pixel có giá trị màu tương tự vào cùng một cụm.

### Các kỹ thuật chính:
- **K-Means trên không gian màu RGB**: Reshape ảnh thành mảng 2D pixel, chuyển sang `float32`, sau đó áp dụng `cv2.kmeans()` với các giá trị $K = 2, 3, 4, 5$ để quan sát mức độ chi tiết phân đoạn.
- **Tiêu chí hội tụ**: Sử dụng `cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER` — dừng khi đạt độ chính xác epsilon hoặc vượt quá số lần lặp tối đa.
- **Hiển thị từng cluster**: Tách các cluster riêng biệt dưới dạng binary mask để phân tích vùng ảnh thuộc mỗi nhóm.

## 4. Region Growing
Thuật toán bắt đầu từ một điểm hạt giống, mở rộng vùng phân đoạn bằng cách thêm các pixel lân cận có giá trị cường độ tương tự trong ngưỡng cho phép.

### Các kỹ thuật chính:
- **BFS-based Region Growing**: Duyệt 8-connected neighbors bằng hàng đợi, kiểm tra chênh lệch cường độ so với seed value — nếu nhỏ hơn threshold thì thêm vào vùng.
- **Thực nghiệm đa seed**: Chọn nhiều seed points khác nhau (tâm ảnh, góc, vùng trên) để quan sát sự phụ thuộc của kết quả vào vị trí khởi đầu.
- **Thực nghiệm đa threshold**: So sánh các ngưỡng $T = 15, 25, 35$ — threshold nhỏ cho vùng chặt chẽ, threshold lớn cho vùng mở rộng hơn.

## 5. Split and Merge
Chia ảnh thành các khối theo cấu trúc quadtree: nếu khối không đồng nhất thì chia tiếp thành 4 phần, sau đó gộp các khối lân cận có tính chất tương tự.

### Các kỹ thuật chính:
- **Quadtree Splitting**: Chia đệ quy ảnh thành các khối vuông, kiểm tra tính đồng nhất bằng độ lệch chuẩn (`np.std`) — nếu vượt ngưỡng thì tiếp tục chia.
- **Merging**: Các khối đồng nhất được gán giá trị trung bình cường độ (`np.mean`) của vùng đó, tạo hiệu ứng phân đoạn dạng block.
- **Thực nghiệm tham số**: So sánh các threshold $T = 10, 20, 30, 50$ với `min_size = 4` — threshold nhỏ cho nhiều chi tiết, threshold lớn cho phân đoạn thô hơn.

## 6. Edge-based Segmentation
Phát hiện cạnh của đối tượng rồi sử dụng biên để xác định ranh giới các vùng phân đoạn.

### Các kỹ thuật chính:
- **Sobel Edge Detection**: Tính gradient theo hai hướng $x$ và $y$ bằng `cv2.Sobel()`, kết hợp thành magnitude $\sqrt{G_x^2 + G_y^2}$.
- **Laplacian Edge Detection**: Dùng đạo hàm bậc hai `cv2.Laplacian()` để phát hiện vùng biến đổi cường độ nhanh — nhạy cảm với nhiễu hơn Sobel.
- **Canny Edge Detection**: Thuật toán đa bước (Gaussian blur → gradient → non-maximum suppression → hysteresis thresholding) với hai ngưỡng $(50, 150)$ và $(100, 200)$ để so sánh mức độ chi tiết biên.
- **Morphological Closing**: Áp dụng `cv2.morphologyEx(MORPH_CLOSE)` để nối các đoạn biên không liên tục, sau đó dùng `cv2.findContours()` + `cv2.drawContours()` để fill vùng bên trong → tạo mask phân đoạn hoàn chỉnh.
- **Overlay kết quả**: Chồng mask phân đoạn lên ảnh gốc RGB để trực quan hóa vùng đã được phân đoạn.

---

# Lab 06: Nhận diện ảnh 

Bài thực hành này tập trung vào kỹ thuật nhận diện đối tượng trong ảnh sử dụng **Haar Cascade Classifier** — bộ phân loại dựa trên Haar-like features được huấn luyện sẵn trong OpenCV.

Script: `LAB06_ImageDetection.ipynb`

## 1. Nhận diện biển báo STOP 
Sử dụng file cascade `stop_data.xml` để phát hiện biển báo STOP trong ảnh.

### Các kỹ thuật chính:
- **Haar Cascade Classifier**: Load bộ phân loại bằng `cv2.CascadeClassifier('stop_data.xml')` — mô hình đã được huấn luyện trên hàng ngàn ảnh positive/negative.
- **Multi-scale Detection**: Hàm `detectMultiScale()` quét ảnh ở nhiều tỉ lệ khác nhau với tham số `minSize=(20, 20)` để phát hiện đối tượng có kích thước khác nhau.
- **Bounding Box**: Vẽ hình chữ nhật xanh lá bằng `cv2.rectangle()` quanh mỗi đối tượng được phát hiện.

## 2. Nhận diện khuôn mặt
Sử dụng `haarcascade_frontalface_default.xml` để nhận diện khuôn mặt trong ảnh nhóm.

### Các kỹ thuật chính:
- **Face Cascade**: Load `cv2.CascadeClassifier('haarcascade_frontalface_default.xml')` — bộ phân loại chuyên biệt cho khuôn mặt nhìn thẳng.
- **Tham số detectMultiScale**:
  - `scaleFactor`: Hệ số thu nhỏ ảnh ở mỗi scale — giá trị nhỏ hơn 1.05 cho kết quả chính xác hơn nhưng chậm hơn.
  - `minNeighbors`: Số lượng neighbor tối thiểu để giữ detection — giá trị lớn hơn giúp giảm false positive.
  - `minSize`: Kích thước pixel tối thiểu của đối tượng cần phát hiện.
- **Thực nghiệm tham số**: So sánh 4 bộ tham số khác nhau để đánh giá ảnh hưởng đến số lượng và chất lượng detection.
- **Cắt khuôn mặt**: Trích xuất từng vùng khuôn mặt đã detect bằng array slicing `img[y:y+h, x:x+w]` để hiển thị riêng biệt.
