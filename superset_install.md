# Cài đặt superset bằng docker

1. **Clone superset repo**: Thực hiện clone repo superset từ github

    ```shell
	git clone --depth=1  https://github.com/apache/superset.git
    ```

2. **Cài đặt bằng docker-compose**: 
* Option 1: Thực hiện trực tiếp:
    ```shell
	cd superset
	docker compose up
    ```
* Option 2: Tạo  immutable images từ local branch
    ```shell
	cd superset
	docker compose -f docker-compose-non-dev.yml up
    ```
 * Option 3: boot up an official release:
     ```shell
     	cd superset
	export TAG=3.1.1
	docker compose -f docker-compose-image-tag.yml up
    ```

3. **Acesss Superset container**:
    ```shell
	docker exec -it superset_app bash
    ```
    
4. **Cài đặt admin user**: Ví dụ tạo 1 user quyền admin:
    ```shell
	superset fab create-admin \
	    --username admin \
	    --firstname first_name \
	    --lastname last_name \
	    --email new_admin@example.com \
	    --password password
    ```
5. **Khởi tạo database cho superset**: 
    ```shell
	superset db upgrade
    ```

6. **Optional: Load các dữ liệu và dashboard mẫu cho superset**:
    ```shell
	superset load_examples
	superset init
    ```

7. **Đăng nhập vào Superset**: Đăng nhập vào http://localhost:8088 và nhập user password để có thể đăng nhập vào superset.

8. **Kết nối vào database**: Khi ta chạy Superset bằng Docker hoặc Docker Compose, Superset sẽ chạy bên trong container Docker riêng của nó, giống như thể Superset đang chạy trên một máy riêng biệt. Vì vậy, khi ta cố gắng kết nối với database local bằng hostname localhost, nó sẽ không hoạt động vì localhost trong trường hợp này chỉ đến container Docker mà Superset đang chạy, chứ không phải là máy chủ thực tế của bạn.

##### Bước 1: Cấu hình MySQL để chấp nhận kết nối từ bên ngoài
   - Chỉnh sửa `my.cnf` để `bind-address` là `0.0.0.0`.
   - Cấp quyền truy cập từ xa cho người dùng MySQL.
   - Khởi động lại MySQL.
#### Bước 2: Kết nối từ Superset

Truy cập vào container Superset:
   - Mở một terminal mới và truy cập vào container Superset:
     ```sh
     docker exec -it superset_app bash
     ```

Cài đặt thư viện MySQL (nếu chưa có):
   - Cài đặt thư viện MySQL client:
     ```sh
     pip install mysqlclient
     ```

Truy cập vào giao diện Superset:
   - Mở trình duyệt và truy cập vào giao diện Superset tại `http://localhost:8088`.
   - Đăng nhập bằng tài khoản Admin.

Thêm kết nối đến MySQL:
   - Đi đến menu **Databases**.
   - Nhấn vào nút **+ Database**.
   - Điền thông tin kết nối:
     - **Database**: Chọn MySQL.
     - **SQLAlchemy URI**: Điền URI kết nối đến MySQL. Thay `username`, `password`, `hostname`, và `dbname` bằng thông tin cụ thể của bạn:
       ```sh
       mysql://username:password@host.docker.internal:3306/dbname
       ```
       Trên Linux, nếu `host.docker.internal` không hoạt động, bạn có thể sử dụng IP của `docker0` interface:
       ```sh
       mysql://username:password@172.18.0.1:3306/dbname
       ```

Kiểm tra kết nối:
   - Nhấn vào nút **Test Connection** để kiểm tra kết nối. Nếu mọi thứ được cấu hình đúng, bạn sẽ thấy thông báo kết nối thành công.



9. **Tạo user và phân quyền**: Trong Apache Superset, quyền (permissions) được quản lý thông qua các vai trò (roles). Mỗi vai trò có thể có một hoặc nhiều quyền, và người dùng có thể được gán một hoặc nhiều vai trò. Dưới đây là một số vai trò và quyền chính trong Superset:

Các Vai Trò Chính

###### Admin:
- Quản lý toàn bộ hệ thống Superset.
- Có tất cả các quyền, bao gồm quản lý người dùng, cơ sở dữ liệu, và cấu hình hệ thống.

###### Alpha:
- Quản lý hầu hết các tính năng, ngoại trừ một số quyền quản trị hệ thống.
- Có thể tạo và chỉnh sửa dashboards, charts, và datasets.

###### Gamma:
- Quyền đọc-only trên hầu hết các đối tượng.
- Có thể xem dashboards và charts, nhưng không thể chỉnh sửa chúng.

###### Public:
- Quyền rất hạn chế, chủ yếu cho phép xem một số nội dung công khai mà không cần đăng nhập.

Các Quyền Cơ Bản
- **Can Dashboard on Superset**: Quyền truy cập vào các dashboard.
- **Can Slice on Superset**: Quyền truy cập vào các biểu đồ (charts).
- **Can Explore on Superset**: Quyền truy cập vào giao diện khám phá dữ liệu.
- **Can SQL Lab on Superset**: Quyền truy cập vào SQL Lab để viết và thực thi truy vấn SQL.
- **Can Edit on [Model]**: Quyền chỉnh sửa các đối tượng như bảng dữ liệu, charts, dashboards.
- **Can Add on [Model]**: Quyền thêm mới các đối tượng như bảng dữ liệu, charts, dashboards.
- **Can Delete on [Model]**: Quyền xóa các đối tượng như bảng dữ liệu, charts, dashboards.
- **Database Access**: Quyền truy cập vào một cơ sở dữ liệu cụ thể.
- **Datasource Access on [datasource]**: Quyền truy cập vào một nguồn dữ liệu cụ thể.
- **Schema Access on [schema]**: Quyền truy cập vào một schema cụ thể trong cơ sở dữ liệu.

Cách Quản Lý Quyền và Vai Trò

###### Thêm vai trò mới:
1. Truy cập vào menu **Security** > **List Roles**.
2. Nhấn vào nút **+ Role** để thêm vai trò mới.
3. Đặt tên cho vai trò và gán các quyền tương ứng.

###### Chỉnh sửa quyền của vai trò:
1. Truy cập vào menu **Security** > **List Roles**.
2. Chọn vai trò bạn muốn chỉnh sửa.
3. Thêm hoặc xóa các quyền từ danh sách quyền.

###### Gán vai trò cho người dùng:
1. Truy cập vào menu **Security** > **List Users**.
2. Chọn người dùng bạn muốn chỉnh sửa.
3. Trong phần **Roles**, chọn các vai trò bạn muốn gán cho người dùng đó.




    
