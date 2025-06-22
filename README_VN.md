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