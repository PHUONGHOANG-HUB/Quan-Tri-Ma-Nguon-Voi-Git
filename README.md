# 🛠️ BÀI THỰC HÀNH: KỸ NĂNG XỬ LÝ SỰ CỐ VÀ QUẢN TRỊ MÃ NGUỒN VỚI GIT

**Môn học:** CS073  
**Thời lượng dự kiến:** 60 phút  
**Mục tiêu:** Sinh viên hiểu rõ cơ chế hoạt động của hệ thống quản trị phiên bản Git, đồng thời thành thạo các kỹ năng xử lý 4 tình huống lỗi nghiêm trọng và phổ biến nhất trong quá trình làm việc nhóm.

---

## 🎯 I. BAREM ĐÁNH GIÁ (10 ĐIỂM)
Sinh viên hoàn thành các tình huống dưới đây và báo cáo kết quả bằng lệnh `git log --oneline --graph --all` để giảng viên nghiệm thu.

* **Tình huống 1 (3 điểm):** Xử lý thành công xung đột mã nguồn (Merge Conflict).
* **Tình huống 2 (2 điểm):** Giải quyết trạng thái "Detached HEAD" an toàn.
* **Tình huống 3 (2 điểm):** Hoàn tác thành công commit chứa dữ liệu nhạy cảm mà không làm mất mã nguồn.
* **Tình huống 4 (3 điểm):** Vượt qua lỗi từ chối đẩy mã nguồn (Push Rejected) khi làm việc nhóm.

🚨 **Điểm phạt (Penalty):** Trừ 2 điểm nếu sinh viên sử dụng lệnh `git push --force` (ghi đè lịch sử hệ thống) hoặc tự ý xóa thư mục dự án để clone lại từ đầu.

---

## 💻 II. YÊU CẦU CHUẨN BỊ (MÔI TRƯỜNG LOCAL)
Khởi động Terminal (hoặc Git Bash), tạo một thư mục làm việc mới và khởi tạo Repository cục bộ (Local Repository) để bắt đầu bài thực hành:

```bash
mkdir Git_Lab_Troubleshooting
cd Git_Lab_Troubleshooting
git init
echo "Tai lieu huong dan du an" > README.md
git add README.md
git commit -m "init: Khoi tao du an"
```

---

## ⚙️ III. NỘI DUNG THỰC HÀNH

### 💥 TÌNH HUỐNG 1: XỬ LÝ XUNG ĐỘT MÃ NGUỒN (MERGE CONFLICT)
**Mô tả:** Sự cố xảy ra khi hai nhánh (branch) cùng chỉnh sửa trên một tệp tin tại cùng một vị trí, dẫn đến việc Git không thể tự động gộp (merge) mã nguồn.

**Bước 1: Cố tình tạo ra xung đột**
Thực thi tuần tự các lệnh sau:
```bash
# Nhánh dev1 tiến hành chỉnh sửa
git checkout -b dev1
echo "Noi dung do Dev 1 cap nhat" > app.txt
git add app.txt
git commit -m "feat: Dev 1 cap nhat app.txt"

# Quay lại nhánh main, tạo nhánh dev2 và chỉnh sửa CÙNG tệp app.txt
git checkout main
git checkout -b dev2
echo "Noi dung do Dev 2 cap nhat" > app.txt
git add app.txt
git commit -m "feat: Dev 2 cap nhat app.txt"

# Tiến hành gộp nhánh vào main
git checkout main
git merge dev1   # Lệnh thực thi thành công
git merge dev2   # Hệ thống báo lỗi Merge Conflict
```

**Bước 2: Hướng dẫn khắc phục**
1. Mở tệp `app.txt` bằng Text Editor (VD: Visual Studio Code).
2. Tìm các dấu hiệu xung đột do Git tự động chèn vào (`<<<<<<<`, `=======`, `>>>>>>>`).
3. Sinh viên tự quyết định giữ lại đoạn mã của `dev1`, của `dev2`, hoặc kết hợp cả hai.
4. Xóa bỏ hoàn toàn các ký tự đánh dấu xung đột của Git.
5. Lưu tệp và hoàn tất quá trình gộp nhánh bằng lệnh:
```bash
git add app.txt
git commit -m "fix: Giai quyet xung dot giua dev1 va dev2"
```

---

### 👻 TÌNH HUỐNG 2: XỬ LÝ TRẠNG THÁI "DETACHED HEAD"
**Mô tả:** Xảy ra khi lập trình viên di chuyển (checkout) trực tiếp đến một commit cụ thể trong quá khứ thay vì chỉ định một nhánh. Trạng thái này sẽ không lưu lại các thay đổi mới nếu không khởi tạo nhánh rẽ.

**Bước 1: Mô phỏng sự cố**
```bash
# Tạo thêm lịch sử commit
echo "Phien ban 1.1" >> README.md
git commit -am "chore: Cap nhat phien ban 1.1"
echo "Phien ban 1.2" >> README.md
git commit -am "chore: Cap nhat phien ban 1.2"

# Xem lịch sử và copy một mã Hash bất kỳ trong quá khứ (VD: a1b2c3d)
git log --oneline

# Di chuyển con trỏ HEAD về commit trong quá khứ (Thay <commit-hash> bằng mã thực tế)
git checkout <commit-hash>
```

**Bước 2: Hướng dẫn khắc phục**
Hệ thống sẽ hiện cảnh báo `You are in 'detached HEAD' state`. Sinh viên áp dụng một trong hai phương án sau:
* **Phương án 1 (Chỉ xem quá khứ, quay về hiện tại):** ```bash
  git checkout main
  ```
* **Phương án 2 (Phục hồi và phát triển tiếp từ quá khứ):** Tạo một nhánh mới tại chính thời điểm này.
  ```bash
  git checkout -b feature/phuc-hoi-ma-nguon
  ```

---

### ⏪ TÌNH HUỐNG 3: HOÀN TÁC COMMIT SAI (SOFT RESET)
**Mô tả:** Lập trình viên vô tình commit thông tin nhạy cảm (mật khẩu cơ sở dữ liệu, API key) hoặc commit khi mã nguồn chưa hoàn thiện. Cần hủy bỏ commit nhưng phải giữ nguyên tệp tin để chỉnh sửa lại.

**Bước 1: Mô phỏng sự cố lộ thông tin**
```bash
git checkout main
echo "DB_PASSWORD=123456" > .env
git add .env
git commit -m "chore: Them file cau hinh .env (Chua mat khau that)"
```

**Bước 2: Hướng dẫn khắc phục**
Sử dụng tham số `--soft` để xóa commit gần nhất khỏi lịch sử nhưng giữ nguyên các tệp tin trong vùng chờ (Staging Area):
```bash
# Lùi lại 1 bước commit
git reset --soft HEAD~1

# Mở tệp .env, tiến hành xóa bỏ mật khẩu nhạy cảm
echo "DB_PASSWORD=********" > .env

# Commit lại với dữ liệu đã được làm sạch
git add .env
git commit -m "chore: Them file cau hinh .env an danh"
```
*(Lưu ý: Chỉ áp dụng giải pháp này khi commit lỗi chưa được đẩy (push) lên Remote Repository).*

---

### 🚫 TÌNH HUỐNG 4: LỖI TỪ CHỐI ĐẨY MÃ NGUỒN (PUSH REJECTED)
**Mô tả:** Xảy ra khi kho lưu trữ từ xa (Remote Repository/GitHub) đang chứa các thay đổi mới hơn so với kho lưu trữ cục bộ (Local Repository). Hệ thống sẽ chặn lệnh `push` để tránh ghi đè dữ liệu của thành viên khác.

**Bước 1: Mô phỏng sự cố**
1. Đăng nhập vào trang web GitHub.com, truy cập Repository của nhóm.
2. Thêm một tệp tin mới (VD: `remote_update.txt`) và nhấn commit trực tiếp trên giao diện web.
3. Trở lại Terminal ở máy tính cục bộ (Local), tiến hành thay đổi mã nguồn độc lập và cố gắng đẩy lên:
```bash
echo "Cap nhat chieu nay tai local" > local_update.txt
git add local_update.txt
git commit -m "feat: Them tinh nang tai local"

# Thử đẩy mã nguồn lên server (Sẽ bị hệ thống báo lỗi Rejected)
git push origin main
```

**Bước 2: Hướng dẫn khắc phục**
Quyên tắc bắt buộc trong làm việc nhóm: **Luôn đồng bộ (pull) trước khi đẩy (push)**.
```bash
# 1. Tải toàn bộ mã nguồn mới nhất từ Remote về Local để đối chiếu
git pull origin main

# 2. Hệ thống sẽ tự động gộp (Nếu có xung đột, hãy áp dụng cách giải quyết của Tình huống 1).

# 3. Đẩy lại mã nguồn đã được đồng bộ lên Remote Repository
git push origin main
```

---
**✅ NGHIỆM THU TIẾN ĐỘ:** Sau khi hoàn tất 4 tình huống, sinh viên gõ lệnh dưới đây và giữ nguyên màn hình để giảng viên kiểm tra biểu đồ lịch sử Git:
```bash
git log --oneline --graph --all
```
