# # Cài đặt Apache Superset từ source

### 1. System Requirements

* Python 3.8+
* Node.js (LTS version) (Nếu chưa cài đặt thì xem hướng dẫn ở phía dưới)
* npm (Đi kèm với Node.js) (Nếu chưa cài đặt thì xem hướng dẫn ở phía dưới)
* Database MySQL hoặc Postgre.

**Cài đặt một số thư viện dựa trên OS**
* Debian và Ubuntu
    ```shell
    sudo apt-get install build-essential libssl-dev libffi-dev python-dev python-pip libsasl2-dev libldap2-dev default-libmysqlclient-dev
    ```
    
* Ubuntu 20.04+
    ```shell
    sudo apt-get install build-essential libssl-dev libffi-dev python3-dev python3-pip libsasl2-dev libldap2-dev default-libmysqlclient-dev
    ```
    
* Fedora và RHEL-derivative Linux distributions
Cài đặt bằng yum
    ```shell
    sudo yum install gcc gcc-c++ libffi-devel python-devel python-pip python-wheel openssl-devel cyrus-sasl-devel openldap-devel
    ```
    
trong một số phiên bản của Fedora, chúng ta cần cài đặt qua `dnf`:
    ```shell
    sudo dnf install gcc gcc-c++ libffi-devel python3-devel python3-pip python3-wheel openssl-devel cyrus-sasl-devel openldap-devel
    ```
    


### 2. Cài đặt môi trường cho Superset

**2.1. Thực hiện clone repo Superset:**
    ```shell
    git clone --depth=1  https://github.com/apache/superset.git
    
    cd superset
    ```

**2.2. Tạo và kích hoạt môi trường cho Superset:**
    ```shell
    conda create -n superset python=3.9
    conda activate superset
    ```
    
### 3. Cài đặt Superset

**3.1. Thêm SECRET_KEY** 
* Superset yêu cầu phải có SECRET_KEY để có thể start-up. Chúng ta có thể sinh SECRET_KEY bằng lệnh:
    ```shell
    openssl rand -base64 42
    ```
    
* Sau đó, tạo file superset_config.py trong folder superset để lưu key:
    ```shell
    nano superset_config.py
    ```
    
* Thực hiện điền trong file superset_config.py key vừa tạo. Ví dụ:
    ```shell
    # superset_config.py
    SECRET_KEY = '8liZR0KzsGx4xaNoBXhHNDo+S5iF1zgL1Li1zqFCxeDm2a31Q/Xhz9Yc'
    ```
    
* Sau đó thực hiện thêm file superset_config.py trên vào config_path bằng câu lệnh:
    ```shell
    export SUPERSET_CONFIG_PATH=path/to/superset_config.py
    ```
Ví dụ: 
    ```shell
    export SUPERSET_CONFIG_PATH=/home/plant27/Documents/VNPT/MyTV_dashboard/demo_superset3/superset/superset_config.py
    ```

**3.2. Cài đặt Superset bằng file Makefile:**
    ```shell
    make install
    ```
    
**3.3. Khởi tạo database cho Superset**

* Upgrade database:
    ```shell
    superset db upgrade
    ```
    
* Tạo user với quyền admin
    ```shell
    export FLASK_APP=superset
    superset fab create-admin
    ```
    
* Khởi tạo Superset:
    ```shell
    superset init
    ```
    
**3.4. Load các dataset sample (Optional)**
    ```shell
    superset load_examples
    ```
    
### 4. Cài đặt Frontend cho Superset

**4.1. Cài đặt Node.js và npm**

* Nếu `nvm` (Node Version Manager) chưa được cài đặt. thì cài đặt theo lệnh sau
    ```shell
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
    ```
* Sau đó đóng và mở lại terminal và chạy:
    ```shell
    export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
    ```

* Cài đặt Node.js version LTS bằng nvm
    ```shell
    nvm install --lts
    ```
* Kiểm tra Node.js và npm
    ```shell
    node -v
    npm -v
    ```

**4.2. Cài đặt Frontend cho Superset**    
Thực hiện cài đặt trong folder superset-frontend:
    ```shell
    cd superset-frontend
    npm install
    npm run build
    ```
    
### 5. Chạy Superset
Chạy câu lệnh: 
    ```shell
    superset run -p 8088 --with-threads --reload --debugger
    ```
và đăng nhập vào superset bằng link `http://localhost:8088`
