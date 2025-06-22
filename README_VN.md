Minh hoạ cách sử dụng uv trong việc cài đặt và sử dụng thư viện

------------------
## 1. Cách sử dụng uv cài đặt vào .venv
Trong project có thể có nhiều phiên bản, việc tồn tại nhiều version của một thư viện trong các version là bình thường

Khi đó uv hỗ trợ việc quản lý các version này, kết hợp với conda để tạo ra cách hoạt động tốt hơn

1. tạo môi trường ảo bằng conda
2. cài đặt thư viện bằng uv trên môi trường ảo đó
3. dùng uv để khởi tạo các project mới: uv init <project_name>
4. trong các version project: thực hiện cài đặt các thư viện bằng uv add <package_name>. nó sẽ tạo ra thư mục .venv trong thư mục project, các package này độc lập với môi trường conda đã tạo lúc đầu
5. sau đó nếu muốn sync các thư viện trong project với môi trường `venv` thì thực hiện lệnh uv sync. Nếu muốn uv cài đặt các thư viện trong môi trường conda, có thể dùng cách sau: `uv pip install . --system`. một cách nữa là dùng `requirements.txt`, đề cập sau 
    - . đại diện cho thư mục dự án hiện tại.
    - uv pip install . sẽ đọc pyproject.toml, giải quyết các dependencies (có tham khảo uv.lock để lấy các phiên bản đã khóa), và sau đó cài đặt dự án cùng các dependencies đó.
    - --system sẽ hướng việc cài đặt vào môi trường Conda (uv-example) đang hoạt động


chú ý: cần cấu hình cache dir để giảm tải lưu trữ trên ổ cứng chính
- UV_CACHE_DIR
    - trên windows thì cầu hình ở Environment Variables -> System variable
    - trên linux thì cấu hình ở ~/.bashrc: bằng export UV_CACHE_DIR=/path/to/cache
- các biến môi trường khác thao tác tương tự, cần hiểu rõ về tác dụng của chung, tham khảo tại [đây](https://docs.astral.sh/uv/reference/environment/)

khi tiếp nhận một project mới cần làm những việc sau:
1. tạo môi trường ảo bằng conda
2. activate môi trường ảo đó
3. tạo và cập nhập uv.lock: `uv lock`: lệnh này sẽ tạo ra file uv.lock trong thư mục hiện tại, phân tích và giải quyết các phụ thuộc
4. đồng bộ hoá môi trường ảo (.venv) với uv.lock:
   - phải chạy lệnh uv add từng package
   - chưa tìm ra cách khác
   - uv pip install -r pyproject.toml (cài đặt trực tiếp vào môi trường conda)

--------------
## 2. Cài đặt trực tiếp vào môi trường conda
Nếu không quan tâm đến version của code, mà muốn cài đặt trực tiếp vào môi trường conda thì thực hiện như sau (mục tiêu là cần dùng uv để cài đặt)

1. tạo môi trường ảo bằng conda
2. tạo project [có những cách sau](https://docs.astral.sh/uv/guides/projects/)
   - cách 1: uv init <project_name>, cd <project_name>
   - cách 2: mkdir <project_name>, cd <project_name>, uv init
   - project structure sẽ như sau:
```commandline
.
├── .venv
│   ├── bin
│   ├── lib
│   └── pyvenv.cfg
├── .python-version
├── README.md
├── main.py
├── pyproject.toml
└── uv.lock
```
3. thực hiện cài đặt trực tiếp vào conda: việc này không được khuyến khích vì nó sẽ gây ra các vấn đề về conflict giữa các thư viện trong conda và các thư viện trong project, tuy nhiên nếu ý định của bạn là như vậy thì dưới đây là hướng dẫn
   - uv pip install <package_name>
   - uv pip install -r requirements.txt
   - uv pip install . (cài đặt trực tiếp vào môi trường conda, không tạo ra .venv)

```commandline
uv pip install [OPTIONS] <PACKAGE|--requirements <REQUIREMENTS>|--editable <EDITABLE>|--group <GROUP>>
```
   - uv pip install . --system
   - uv pip install -r pyproject.toml --system
   - uv pip install <package_name> --system

`--system` nghĩa là:
- Install packages into the system Python environment.
- By default, uv installs into the virtual environment in the current working directory or any parent directory
- option instructs uv to instead use the first Python found in the system PATH

hoặc khi đang activate môi trường thì việc cài đặt được ưu tiên vào môi trường ảo đó, cho dù là của `conda` hay `venv`, nghĩa là trong trường hợp này không cần `--system` nữa.

_chú ý_:
- nó sẽ không thực hiện thay đổi `pyproject.toml` hay `uv.lock`, vì vậy nếu bạn muốn quản lý các thư viện trong project thì cần thực hiện các lệnh `uv add <package_name>`,...  để cập nhật các file này.
- vì nó không tự nhớ các version của dependencies, nên cần phải cung cấp chính xác version của package, nếu không sẽ có thể xảy ra lỗi khi chạy code do các version không tương thích.
