# Route Planning Project

This repo contains the starter code for the Route Planning project.

<img src="map.png" width="600" height="450" />

## Cloning

When cloning this project, be sure to use the `--recurse-submodules` flag. Using HTTPS:
```
git clone https://github.com/udacity/CppND-Route-Planning-Project.git --recurse-submodules
```
or with SSH:
```
git clone git@github.com:udacity/CppND-Route-Planning-Project.git --recurse-submodules
```

## Dependencies for Running Locally
* cmake >= 3.11.3
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux, Mac), 3.81 (Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 7.4.0
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same instructions as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* IO2D
  * Installation instructions for all operating systems can be found [here](https://github.com/cpp-io2d/P0267_RefImpl/blob/master/BUILDING.md)
  * This library must be built in a place where CMake `find_package` will be able to find it
 

## Compiling and Running

### Compiling
To compile the project, first, create a `build` directory and change to that directory:
```
mkdir build && cd build
```
From within the `build` directory, then run `cmake` and `make` as follows:
```
cmake ..
make
```
### Running
The executable will be placed in the `build` directory. From within `build`, you can run the project as follows:
```
./OSM_A_star_search
```
Or to specify a map file:
```
./OSM_A_star_search -f ../<your_osm_file.osm>
```

## Testing

The testing executable is also placed in the `build` directory. From within `build`, you can run the unit tests as follows:
```
./test
```

## Troubleshooting
* Some students have reported issues in cmake to find io2d packages, make sure you have downloaded [this](https://github.com/cpp-io2d/P0267_RefImpl/blob/master/BUILDING.md#xcode-and-libc).
* For MAC Users cmake issues: Comment these lines from CMakeLists.txt under P0267_RefImpl
    ```
    if( NOT DEFINED IO2D_WITHOUT_SAMPLES )
	     add_subdirectory(P0267_RefImpl/Samples)
    endif()
    ```
    And then run "ALL_Build" and "install" in XCode.
    
    If any packages are missing try to install packages using 
    ```
    brew install pkg-config
    ```
 * For Ubuntu Linux IO2D installation errors, follow the given steps:
   ```
	sudo apt update
	sudo apt install build-essential
	sudo apt install cmake
	sudo apt install libcairo2-dev
	sudo apt install libgraphicsmagick1-dev
	sudo apt install libpng-dev

	git clone --recurse-submodules https://github.com/cpp-io2d/P0267_RefImpl
	cd P0267_RefImpl
	mkdir Debug
	cd Debug
	cmake --config Debug "-DCMAKE_BUILD_TYPE=Debug" ..
	cmake --build .
	sudo make install
   ```
     
 * If you are working on windows and unable to install IO2D:
      * Enable WSL (Windows Subsystem for Linux) and use a distribution like [Ubuntu](https://ubuntu.com/wsl).(available from the windows store): 
      * Install the required dependencies (compiler, cmake etc.) in the WSL(as mentioned above for ubuntu)
      * Configure CLion to use the WSL [toolchain](https://www.jetbrains.com/help/clion/how-to-use-wsl-development-environment-in-product.html#wsl-tooclhain)
      * Use the WSL toolchain to build the project
      * If you are still facing errors, visit this [link](https://github.com/udacity/CppND-Route-Planning-Project/issues/9).
     

* If you are facing errors with --config try to remove -- from the command.


## Data flow
Chương trình này là một ứng dụng lập kế hoạch tuyến đường (Route Planner) sử dụng thuật toán A* để tìm đường đi ngắn nhất trên bản đồ OSM (OpenStreetMap). Dưới đây là giải thích chi tiết về **luồng dữ liệu (data flow)** của chương trình:

---

### **1. Nhập dữ liệu bản đồ**
- **Nguồn dữ liệu**:
  - Dữ liệu bản đồ được lưu trong các tệp `.osm` (ví dụ: hochiminh.osm, map.osm).
  - Các tệp này chứa thông tin về các nút (nodes), đường (ways), và quan hệ (relations) trong định dạng XML.
- **Quá trình nhập**:
  - Hàm `ReadFile()` trong `main.cpp` được sử dụng để đọc tệp `.osm` và tải dữ liệu vào bộ nhớ.
  - Thư viện `pugixml` được sử dụng để phân tích cú pháp (parse) dữ liệu XML và xây dựng cấu trúc cây DOM trong bộ nhớ.

---

### **2. Xử lý dữ liệu bản đồ**
- **Chuyển đổi dữ liệu**:
  - Dữ liệu từ tệp `.osm` được chuyển đổi thành một mô hình bản đồ (`RouteModel`) trong chương trình.
  - Lớp `RouteModel` (được định nghĩa trong `src/route_model.cpp`) mở rộng lớp `Model` để thêm các chức năng liên quan đến thuật toán tìm đường.
- **Tìm nút gần nhất**:
  - Hàm `FindClosestNode()` trong `RouteModel` được sử dụng để tìm các nút gần nhất với vị trí bắt đầu và kết thúc do người dùng chỉ định.
  - Điều này giúp xác định điểm bắt đầu và điểm kết thúc trong thuật toán A*.

---

### **3. Tìm đường đi ngắn nhất (A* Search)**
- **Khởi tạo thuật toán**:
  - Lớp `RoutePlanner` (được định nghĩa trong `src/route_planner.cpp`) thực hiện thuật toán A*.
  - Hàm `AStarSearch()` được gọi từ `main()` để bắt đầu quá trình tìm kiếm.
- **Quá trình tìm kiếm**:
  - **Danh sách mở (`open_list`)**:
    - Lưu trữ các nút cần được kiểm tra.
    - Nút có giá trị `f = g + h` nhỏ nhất được chọn để kiểm tra tiếp theo (hàm `NextNode()`).
  - **Tìm nút lân cận**:
    - Hàm `AddNeighbors()` được gọi để tìm các nút lân cận của nút hiện tại.
    - Với mỗi nút lân cận:
      - Tính giá trị `g` (khoảng cách từ nút bắt đầu đến nút hiện tại).
      - Tính giá trị `h` (ước lượng khoảng cách từ nút hiện tại đến nút đích) bằng hàm `CalculateHValue()`.
      - Thêm nút lân cận vào danh sách mở.
  - **Xây dựng đường đi cuối cùng**:
    - Khi tìm thấy nút đích, hàm `ConstructFinalPath()` được gọi để xây dựng đường đi từ nút đích về nút bắt đầu bằng cách lần theo các nút cha (parent).

---

### **4. Xuất dữ liệu**
- **Hiển thị kết quả**:
  - Lớp `Render` (được định nghĩa trong `src/render.cpp`) được sử dụng để hiển thị bản đồ và đường đi ngắn nhất.
  - Kết quả được vẽ lên hình ảnh bản đồ (ví dụ: hochiminh.png hoặc map.png).
- **Lưu kết quả**:
  - Nếu cần, kết quả có thể được lưu lại dưới dạng tệp hoặc in ra màn hình.

---

### **5. Kiểm thử**
- **Thư viện Google Test**:
  - Các tệp trong thư mục googletest được sử dụng để kiểm thử các thành phần của chương trình.
  - Ví dụ: Các bài kiểm thử trong test_document.cpp hoặc test_write.cpp kiểm tra tính đúng đắn của việc xử lý dữ liệu XML.

---

### **Tóm tắt luồng dữ liệu**
1. **Nhập dữ liệu**:
   - Tệp `.osm` được đọc và phân tích cú pháp để tạo cấu trúc cây DOM.
   - Dữ liệu được chuyển đổi thành mô hình bản đồ (`RouteModel`).
2. **Xử lý dữ liệu**:
   - Tìm nút gần nhất với vị trí bắt đầu và kết thúc.
   - Thực hiện thuật toán A* để tìm đường đi ngắn nhất.
3. **Xuất dữ liệu**:
   - Hiển thị kết quả trên bản đồ hoặc lưu kết quả vào tệp.
4. **Kiểm thử**:
   - Sử dụng Google Test để đảm bảo tính đúng đắn của các thành phần.