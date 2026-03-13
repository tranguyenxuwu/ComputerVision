# Lab 04: Keypoint Detection

Bài thực hành này tập trung vào các kỹ thuật phát hiện điểm đặc trưng (Keypoint Detection), trích xuất đặc trưng và ứng dụng trong ghép ảnh, truy vấn ảnh sử dụng OpenCV, NumPy.

Script: `Lab04_KeypointDetection.ipynb`

## 1. Khai báo thư viện & Đọc ảnh
Sử dụng OpenCV đọc ảnh (`cv2.imread`) và chuyển đổi kênh màu BGR → RGB (`img[:,:,::-1]`) để hiển thị đúng với `matplotlib`. Ảnh được chuyển sang grayscale và `float32` để phục vụ các thuật toán phía sau.

## 2. Harris Corner Detection
Phát hiện các góc (corner) trong ảnh bằng thuật toán Harris.

### Các kỹ thuật chính:
- **`cv2.cornerHarris()`**: Tính Harris corner response map từ ảnh xám `float32` với các tham số `blockSize`, `ksize` (Sobel kernel), và `k` (Harris free parameter ≈ 0.04).
- **Thresholding**: Lọc các điểm có response > 1% giá trị max để xác định vị trí corner.
- **`cv2.cornerSubPix()`**: Tinh chỉnh vị trí corner ở mức sub-pixel bằng phương pháp lặp, cho kết quả chính xác hơn.
- **So sánh tham số**: Thực nghiệm với các giá trị `blockSize` khác nhau (2, 4, 7) để quan sát ảnh hưởng đến số lượng và vị trí corner.

## 3. Band-pass Filtering by Difference of Gaussians (DoG)
Bộ lọc thông dải DoG được dùng để phát hiện các vùng có biến đổi cường độ ở một dải tần số cụ thể.

### Các kỹ thuật chính:
- **Difference of Gaussians**: Lấy hiệu hai ảnh đã làm mờ Gaussian ở hai sigma khác nhau ($\sigma_1 < \sigma_2$), tạo ra bộ lọc band-pass loại bỏ cả tần số quá cao (nhiễu) và quá thấp (nền).
- **Thực nghiệm nhiều cặp sigma**: So sánh các cặp $(1,2)$, $(1,4)$, $(2,6)$, $(3,9)$ để quan sát sự khác biệt trong vùng tần số được giữ lại.
- **Threshold response**: Áp dụng ngưỡng lên giá trị tuyệt đối DoG để xác định keypoints tiềm năng.

## 4. Automatic Scale Selection
Tự động chọn tỉ lệ (scale) tối ưu cho từng điểm trong ảnh bằng **Laplacian of Gaussian (LoG)** chuẩn hóa theo scale.

### Các kỹ thuật chính:
- **Scale-normalized LoG**: Tính $\sigma^2 \cdot \nabla^2 G_\sigma * I$ tại nhiều giá trị $\sigma$ khác nhau, trong đó $\nabla^2$ là toán tử Laplacian.
- **Scale space**: Xây dựng không gian tỉ lệ 3D $(x, y, \sigma)$ chứa LoG response ở mỗi scale.
- **Best scale map**: Tìm $\sigma$ cho response cực đại tại mỗi pixel — cho biết kích thước đặc trưng cục bộ tại đó.

## 5. Scale Invariant Detection
Phát hiện keypoints bất biến theo tỉ lệ bằng cách tìm cực trị cục bộ trong không gian 3D $(x, y, \sigma)$.

### Các kỹ thuật chính:
- **Local extrema detection**: Tìm các điểm có response lớn hơn tất cả 26 neighbor trong khối $3 \times 3 \times 3$ (x, y, scale).
- **Non-maximum suppression**: Chỉ giữ lại các cực trị cục bộ vượt ngưỡng.
- **Scale-proportional visualization**: Vẽ mỗi keypoint dưới dạng vòng tròn có bán kính tỷ lệ với sigma, thể hiện kích thước đặc trưng.

## 6. Scale-space Blob Detector
Phát hiện blob (vùng đồng nhất) ở nhiều tỉ lệ bằng xấp xỉ LoG thông qua DoG — nguyên lý cơ bản của SIFT.

### Các kỹ thuật chính:
- **LoG approximation by DoG**: Sử dụng tỉ lệ $k \approx 1.6$ (theo bài báo SIFT gốc) và nhân với $\sigma^2$ để chuẩn hóa.
- **3D local maxima**: Tìm cực đại trong khối $(3 \times 7 \times 7)$ qua các scale liền kề.
- **So sánh OpenCV SimpleBlobDetector**: Đối chiếu kết quả custom với `cv2.SimpleBlobDetector`.

## 7. Bag-of-Words Detection
Mô phỏng mô hình Bag-of-Words (BoW) với ORB descriptors.

### Các kỹ thuật chính:
- **ORB Keypoint Detection**: Trích xuất keypoints và binary descriptors bằng `cv2.ORB_create()`.
- **K-Means Clustering**: Phân cụm descriptors thành $K$ visual words (vocabulary) bằng `cv2.kmeans()`.
- **Histogram Encoding**: Đếm tần suất xuất hiện của mỗi visual word → tạo vector BoW đại diện cho ảnh.

## 8. Image Panoramas
Ghép hai ảnh thành panorama bằng feature matching và homography.

### Các kỹ thuật chính:
- **Feature Matching**: Dùng `cv2.BFMatcher(NORM_HAMMING)` + cross check để tìm cặp keypoints tương ứng giữa hai ảnh.
- **Homography Estimation**: Tính ma trận Homography $3 \times 3$ bằng `cv2.findHomography()` với RANSAC để loại bỏ outliers.
- **Image Warping**: Biến đổi phối cảnh ảnh phải sang hệ tọa độ ảnh trái bằng `cv2.warpPerspective()`, sau đó ghép lại thành ảnh panorama hoàn chỉnh.

## 9. Automatic Mosaicing
Ghép nhiều mảnh ảnh (tiles) tự động thành ảnh tổng thể.

### Các kỹ thuật chính:
- **Chia ảnh thành tiles**: Tạo nhiều mảnh có vùng chồng lấp 30%.
- **OpenCV Stitcher**: Sử dụng `cv2.Stitcher_create()` để ghép tự động — xử lý feature detection, matching, bundle adjustment và blending nội bộ.
- **Manual Homography Chain**: Phương pháp thay thế ghép tuần tự từng tile bằng chuỗi homography khi Stitcher thất bại.

## 10. Wide Base-line Stereo
Stereo matching giữa hai view có baseline rộng (góc nhìn khác biệt lớn).

### Các kỹ thuật chính:
- **Lowe's Ratio Test**: Lọc matches bằng tỉ lệ khoảng cách giữa best match và second-best match $(d_1 / d_2 < 0.75)$ để loại bỏ matches mơ hồ.
- **Fundamental Matrix**: Tính ma trận cơ bản $F$ bằng `cv2.findFundamentalMat()` với RANSAC — thể hiện ràng buộc epipolar giữa hai view.
- **Epipolar Lines**: Vẽ đường epipolar tương ứng trên mỗi view bằng `cv2.computeCorrespondEpilines()`, minh họa hình học epipolar.

## 11. CBIR (Content-Based Image Retrieval)
Truy vấn ảnh theo nội dung — tìm ảnh tương tự nhất trong database.

### Các kỹ thuật chính:
- **Color Histogram Feature**: Trích xuất histogram màu 3 kênh (BGR) với $32$ bins mỗi kênh → vector đặc trưng $96$-chiều.
- **Texture Feature**: Trích xuất đặc trưng kết cấu dựa trên histogram gradient magnitude (Sobel).
- **Cosine Similarity**: Đo độ tương đồng giữa ảnh query và database bằng cosine similarity $\frac{\mathbf{a} \cdot \mathbf{b}}{|\mathbf{a}||\mathbf{b}|}$.
- **Ranking**: Xếp hạng kết quả theo similarity giảm dần — ảnh gốc luôn có similarity = 1.0, các biến thể nhẹ (sáng/tối) có similarity cao hơn biến thể mạnh (xoay/cắt).

## 12. Bag-of-Words with SIFT + Histogram
Kết hợp SIFT descriptors với mô hình BoW để so sánh ảnh.

### Các kỹ thuật chính:
- **SIFT Descriptor**: Trích xuất 128-chiều SIFT descriptors bằng `cv2.SIFT_create()` — bất biến với scale và rotation.
- **Visual Vocabulary (K-Means)**: Phân cụm $K=30$ visual words từ tập SIFT descriptors.
- **BoW Encoding**: Gán mỗi descriptor trong ảnh mới vào visual word gần nhất (nearest assignment) → đếm histogram.
- **L2 Normalization**: Chuẩn hóa histogram BoW để so sánh công bằng giữa các ảnh có số keypoints khác nhau.
- **So sánh biến thể**: Tính cosine similarity giữa histogram BoW của ảnh gốc và các biến thể (flip, bright, dark, rotated, blurred, cropped) — cho thấy SIFT BoW có tính bất biến tốt hơn color histogram đối với các biến đổi hình học.
