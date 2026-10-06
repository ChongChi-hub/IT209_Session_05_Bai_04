# Báo cáo Bài 4: Mô phỏng quy trình Hotfix & Gitflow thực tế

## 1. Mục tiêu
- Áp dụng mô hình phân nhánh Gitflow chuẩn trong quản lý vòng đời phần mềm.
- Thực hành tạo, kiểm thử và tích hợp nhánh Hotfix trực tiếp vào hệ thống đang chạy.
- Đồng bộ hóa bản vá nóng về nhánh phát triển (develop) để tránh trôi lỗi.

## 2. Quá trình thực hiện Gitflow

Theo quy trình Gitflow chuẩn, em đã mô phỏng kịch bản có 2 nhánh chính hoạt động song song là `main` (chứa code Production ổn định) và `develop` (chứa code đang phát triển tính năng mới). 

Khi hệ thống gặp lỗi nghiêm trọng (data leak) trên `main`, em thực hiện quy trình Hotfix như sau:

1. **Tạo nhánh hotfix:** Tách nhánh `hotfix/v1.0.1` trực tiếp từ `main`.
   ```bash
   git checkout main
   git checkout -b hotfix/v1.0.1
   ```

2. **Sửa lỗi:** Thực hiện sửa code và commit trên nhánh hotfix.
   ```bash
   git commit -m "fix data leak"
   ```

3. **Gộp vào main & đánh tag Release:** Quay lại nhánh `main`, merge nhánh hotfix vào và tạo tag phiên bản `v1.0.1` để đánh dấu bản phát hành sửa lỗi.
   ```bash
   git checkout main
   git merge hotfix/v1.0.1
   git tag -a v1.0.1 -m "Release Hotfix 1.0.1"
   ```

4. **Đồng bộ về develop:** Đây là bước quan trọng nhất để tránh "trôi lỗi". Em chuyển qua nhánh `develop` và merge ngược nhánh hotfix vào.
   ```bash
   git checkout develop
   git merge hotfix/v1.0.1 -m "Merge hotfix/v1.0.1 into develop"
   ```

5. **Dọn dẹp:** Cuối cùng, xóa nhánh `hotfix/v1.0.1` ở local.
   ```bash
   git branch -d hotfix/v1.0.1
   ```

## 3. Đồ thị sơ đồ phân nhánh (Git Graph)

Dưới đây là đồ thị commit của repository thể hiện rõ cấu trúc Gitflow vừa thực hiện:

```text
*   e4b0f20 (HEAD -> develop) Merge hotfix/v1.0.1 into develop
|\  
| * 86192fa (tag: v1.0.1, main) fix data leak
* | bdd1838 start new feature
|/  
* 661f21d stable v1.0.0
```

*(Nhìn vào đồ thị ta thấy rõ nhánh hotfix tách từ main, sau khi commit thì chia làm 2 đường merge: một đường về thẳng `main` (có gắn tag `v1.0.1`), một đường merge chéo lên nhánh `develop`)*
