#️**BÀI THỰC HÀNH: KỸ NĂNG ĐỒNG BỘ VÀ XỬ LÝ SỰ CỐ MÃ NGUỒN VỚI GIT & GITHUB**

**Môn học:** CS073  
**Mục tiêu:** Sinh viên làm quen với quy trình làm việc chuyên nghiệp trên GitHub, thành thạo các kỹ năng giải quyết xung đột (Conflict) và phục hồi dữ liệu khi xảy ra sai sót trong quá trình phát triển nhóm.

---

## I. THANG ĐIỂM ĐÁNH GIÁ (10 ĐIỂM)
Giảng viên sẽ nghiệm thu dựa trên lịch sử commit trên GitHub và sơ đồ nhánh (Graph).

| STT | Tình huống xử lý | Trạng thái yêu cầu | Điểm |
|:---:|:---|:---|:---:|
| 1 | **Merge Conflict** | Giải quyết xung đột và `push` thành công lên `main` | 3.0 |
| 2 | **Detached HEAD** | Phục hồi mã nguồn vào nhánh mới và `push` lên GitHub | 2.0 |
| 3 | **Soft Reset** | Hủy commit sai, che giấu thông tin nhạy cảm và `push` lại | 2.0 |
| 4 | **Push Rejected** | Thực hiện `pull`, gộp mã nguồn từ thành viên khác và `push` | 3.0 |

**Lưu ý:** Mọi hành vi sử dụng `git push --force` hoặc xóa Repo để clone lại từ đầu sẽ bị trừ **2.0 điểm**.

---

## II. CHUẨN BỊ MÔI TRƯỜNG (LOCAL)
Sinh viên thực hiện khởi tạo Repository tại máy cục bộ và kết nối với GitHub:

```bash
# Khởi tạo thư mục và repo
mkdir Git_Professional_Lab
cd Git_Professional_Lab
git init

# Tạo file ban đầu và commit
echo "Tài liệu hướng dẫn dự án" > README.md
git add README.md
git commit -m "init: Khởi tạo dự án"

# Kết nối với GitHub (Thay URL bằng link repo của bạn trên Classroom)
git remote add origin <URL_REPO_CỦA_BẠN>
git push -u origin main
```

---

##️ III. NỘI DUNG THỰC HÀNH


### TÌNH HUỐNG 1: XỬ LÝ XUNG ĐỘT MÃ NGUỒN (MERGE CONFLICT)
**Yêu cầu:** Giải quyết xung đột khi hai nhánh cùng tác động vào một dòng code.

1. **Tạo xung đột:**
   ```bash
   git checkout -b dev1
   echo "Nội dung từ Dev 1" > app.txt
   git add . && git commit -m "feat: Dev 1 cập nhật app.txt"
   
   git checkout main
   git checkout -b dev2
   echo "Nội dung từ Dev 2" > app.txt
   git add . && git commit -m "feat: Dev 2 cập nhật app.txt"
   ```
2. **Thực hiện Merge:**
   ```bash
   git checkout main
   git merge dev1   # Thành công
   git merge dev2   # Xuất hiện CONFLICT
   ```
3. **Khắc phục:** Mở `app.txt`, xóa các ký hiệu `<<<<`, `====`, `>>>>`, giữ lại nội dung mong muốn.
4. **Đồng bộ:**
   ```bash
   git add app.txt
   git commit -m "fix: Giải quyết xung đột giữa dev1 và dev2"
   git push origin main
   ```

---

### TÌNH HUỐNG 2: PHỤC HỒI TỪ TRẠNG THÁI "DETACHED HEAD"
**Yêu cầu:** Truy cập một commit cũ trong quá khứ và đưa nó trở thành một nhánh phát triển mới trên GitHub.

1. **Tạo lịch sử:**
   ```bash
   echo "Update 1" >> README.md && git commit -am "chore: Update 1"
   echo "Update 2" >> README.md && git commit -am "chore: Update 2"
   ```
2. **Quay về quá khứ:**
   ```bash
   git log --oneline
   git checkout <mã-hash-của-commit-đầu-tiên>
   ```
3. **Khắc phục và Đồng bộ:**
   ```bash
   # Tạo nhánh mới để giữ lại trạng thái này
   git checkout -b feature/recovery-point
   # Đẩy nhánh mới này lên GitHub
   git push -u origin feature/recovery-point
   ```

---

### TÌNH HUỐNG 3: HOÀN TÁC VÀ LÀM SẠCH DỮ LIỆU (SOFT RESET)
**Yêu cầu:** Hủy commit chứa thông tin nhạy cảm nhưng không làm mất dữ liệu đang viết dở.

1. **Mô phỏng lỗi:**
   ```bash
   git checkout main
   echo "API_KEY=secret_12345" > .env
   git add .env && git commit -m "feat: Cấu hình API Key"
   ```
2. **Khắc phục:**
   ```bash
   # Hủy commit nhưng giữ lại file trong Staging
   git reset --soft HEAD~1
   # Chỉnh sửa lại file cho an toàn
   echo "API_KEY=********" > .env
   git add .env
   git commit -m "fix: Bảo mật thông tin API Key"
   ```
3. **Đồng bộ:**
   ```bash
   git push origin main
   ```

---



### TÌNH HUỐNG 4: QUY TRÌNH PULL - MERGE - PUSH (LÀM VIỆC NHÓM)
**Yêu cầu:** Xử lý lỗi từ chối lệnh `push` khi kho lưu trữ Remote có thay đổi mới từ thành viên khác.

1. **Mô phỏng lỗi từ Remote:** Truy cập GitHub.com, sửa trực tiếp file `README.md` trên giao diện web và Commit.
2. **Tạo thay đổi tại Local:**
   ```bash
   git checkout main
   echo "Thay đổi mới tại máy cục bộ" >> README.md
   git add . && git commit -m "feat: Cập nhật nội dung tại local"
   ```
3. **Thực hiện Đẩy mã nguồn:**
   ```bash
   git push origin main # Sẽ bị báo lỗi [rejected]
   ```
4. **Khắc phục (Quy trình chuẩn):**
   ```bash
   # Tải code mới nhất về
   git pull origin main
   # (Nếu có xung đột, xử lý như Tình huống 1)
   # Đẩy lại lên GitHub
   git push origin main
   ```

---

## NGHIỆM THU
Sau khi hoàn tất, sinh viên giữ nguyên màn hình Terminal với lệnh sau để giảng viên kiểm tra:
```bash
git log --oneline --graph --all
```
