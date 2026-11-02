# Book Audio App

Flutter app đọc sách và nghe audiobook, dùng FastAPI làm backend.

## Chạy backend với SQLite

Không cần cài hoặc chạy MySQL. SQLite được tích hợp trong Python; khi backend khởi động, ứng dụng tự tạo các bảng và file database `backend/ebook_app.sqlite3`.
SQLite lưu chuỗi Unicode bằng UTF-8; không cần đặt charset riêng trong `sqlite3.connect`. FastAPI trả JSON UTF-8 và ứng dụng Flutter cần giải mã response bằng UTF-8. Nếu ký tự đã bị ghi thành `?` vào database, phải cập nhật lại bản ghi bằng chuỗi Unicode đúng vì không thể suy ra ký tự gốc từ dấu hỏi.

Mở PowerShell tại thư mục dự án:

```powershell
cd backend
python -m pip install "fastapi>=0.100" "pydantic>=2" uvicorn python-multipart
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

Mở `http://127.0.0.1:8000/docs` để kiểm tra API. Không cần file SQL mẫu: database mới tự tạo schema, tài khoản quản trị mặc định `admin` / `123`, và bản ghi cho các PDF/MP3 tìm thấy trong `backend/uploads`. Các bản ghi seed được tạo idempotent khi server khởi động, nên nhóm clone repository và chạy backend sẽ thấy cùng thư viện media. Database MySQL cũ không tự chuyển sang SQLite; cần export/import riêng nếu muốn giữ dữ liệu cũ.

Các file tĩnh được phục vụ từ `/uploads`, ví dụ `http://<backend-host>:8000/uploads/ten-file.pdf`.

## Kết nối ứng dụng Android

- **Android Emulator:** địa chỉ `10.0.2.2:8000` trỏ về máy tính chạy backend.
- **Điện thoại thật:** thay `10.0.2.2` bằng IPv4 LAN của máy tính (ví dụ `192.168.1.20`), cho phép cổng TCP `8000` qua Windows Firewall, và bảo đảm điện thoại với máy tính cùng mạng Wi-Fi. Backend cần chạy với `--host 0.0.0.0`.

Hiện địa chỉ backend `10.0.2.2` đang được ghi trực tiếp ở các URL trong `lib/` và URL file được tạo trong `backend/main.py`. Khi dùng điện thoại thật, thay địa chỉ đó bằng IPv4 LAN của máy tính ở các vị trí này, rồi chạy lại ứng dụng Flutter. Không dùng `localhost` trên điện thoại vì địa chỉ đó trỏ về chính điện thoại.

## Chạy Flutter trên Android Emulator

Mở emulator, sau đó tại thư mục gốc dự án chạy:

```powershell
flutter pub get
flutter run
```

Khởi động backend trước khi mở app. Với database mới, đăng nhập bằng tài khoản thử `admin` / `123`; backend sẽ tự thêm danh sách PDF/MP3 từ `backend/uploads`.
