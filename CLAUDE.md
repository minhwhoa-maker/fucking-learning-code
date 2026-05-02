# Fleet App Context

## Stack
- Vanilla HTML/CSS/JS + Supabase + Vercel
- Supabase project: icwmtqfpbefntfxboofr
- Repo: minhwhoa-maker/fucking-learning-code
- Live URL: https://fucking-learning-code.vercel.app

## Files
- bai10.html: Login page (Google OAuth + role redirect)
- owner-dashboard.html: Owner dashboard (trips, stats, filter month, realtime, thumbnail ảnh hóa đơn)
- driver-page.html: Driver trip input (nhập chuyến, tạm ứng, hoàn ứng, upload ảnh hóa đơn — 2 button: chụp ảnh + chọn thư viện)
- driver.html: Driver management (danh sách, lương, tạm ứng tháng, export Excel/PDF)
- vehicles.html: Vehicle management (danh sách xe, trạng thái, bảo dưỡng inline)
- style.css: Shared design system (CSS variables, tất cả components)
- CONTEXT.md: This file

## CSS Design System (style.css)
- Tất cả pages dùng chung `/style.css` — không có `<style>` block riêng
- CSS variables: `--primary #1565c0`, `--danger #e74c3c`, `--success #27ae60`, `--warning #e67e22`, `--bg #f0f2f5`
- Classes: `.btn`, `.btn-danger`, `.btn-success`, `.btn-warning`, `.btn-logout`, `.btn-full`, `.btn-sm`
- Stat values: `.stat-value.green/red/blue/orange` (thay cho `.doanh-thu/.chi-phi/.loi-nhuan/.luong` cũ)

## Database tables
- users (id, email, full_name, sdt, role)
- trips (id, ngay, tuyen_duong, doanh_thu, chi_phi, luong_chuyen, tam_ung, hoan_ung, tai_xe_id, ghi_chu, anh_hoa_don)
- tam_ung_thang (id, tai_xe_id, thang, so_tien, ghi_chu)
- xe (id, bien_so, loai_xe, nam_sx, trang_thai, tai_xe_id)
- bao_duong (id, xe_id, ngay, loai, mo_ta, chi_phi, created_at)

## Supabase anon key
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imljd210cWZwYmVmbnRmeGJvb2ZyIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzY5Mzg3NzgsImV4cCI6MjA5MjUxNDc3OH0.N1gsPt4eZav2LL2XDttqlsAB06b1UzXb4bFTMi3K8NM

## Current status
- Bài 26: Tách shared style.css — hoàn thành
  - Xóa toàn bộ `<style>` block khỏi 5 file HTML
  - Thêm `<meta name="viewport">` + `<link rel="stylesheet" href="/style.css">` vào tất cả
  - Đổi class: `btn-red→btn-danger`, `btn-orange→btn-warning`, `btn-green→btn-success`
  - Đổi stat classes: `doanh-thu/chi-phi/loi-nhuan/luong → stat-value green/red/blue/orange`
  - Còn lỗi chưa fix: xem danh sách lỗi bên dưới

## Known issues (style.css)
- bai10.html: body cần `display:flex; justify-content:center; align-items:center` để center login card
- bai10.html: `.card` cần override `width:320px; text-align:center; padding:40px 36px` cho login card
- bai10.html: `.btn-google` và `#dashboard {display:none}` chưa có trong style.css
- owner-dashboard.html: `.container` cần `background:white; border-radius:12px; box-shadow; overflow-x:auto` (hiện là plain wrapper)
- owner-dashboard.html: `.message/.message.empty` chưa có trong style.css (JS dùng class này)
- owner-dashboard.html: `table {min-width:460px}` bị mất → scroll mobile có thể lỗi
- driver-page.html: `.upload-input {display:none}` bị mất → file inputs hiển thị ra ngoài
- driver-page.html: nút submit cần thêm class `btn-full` để full-width
- driver.html + vehicles.html: `th` header đổi từ xanh đậm sang nhạt theo style.css

## Progress
- Bài 1-7: HTML/CSS/JS cơ bản
- Bài 8: Supabase todo app
- Bài 9: Google OAuth login
- Bài 10: Role-based access
- Bài 11: Redirect theo role
- Bài 12: Owner dashboard
- Bài 13: Driver page
- Bài 14: Responsive mobile
- Bài 15: Realtime update
- Bài 16: Tính lương
- Bài 17: Filter theo tháng
- Bài 18: Quản lý tài xế
- Bài 19: Export Excel + PDF
- Bài 20: Tạm ứng/hoàn ứng
- Bài 21: Quản lý xe
- Bài 22: Bảo dưỡng xe ✓
- Bài 23: Upload ảnh hóa đơn ✓
- Bài 24: Cải tiến UX ảnh hóa đơn (2 button driver, thumbnail owner) ✓
- Bài 25: Đồng bộ style toàn app (font, màu, border-radius, shadow) ✓
- Bài 26: Tách shared style.css, xóa style blocks, đổi class names ✓ (còn known issues)

## Supabase Storage
- Bucket: receipts (cần bật Public access để getPublicUrl hoạt động)
- Path format: {userId}/{timestamp}.{ext}

## Notes
- RLS disabled trên tất cả tables (cần fix sau)
- Anon key đừng share public
- Mỗi lần Claude Code mới → paste file này vào đầu
- Định dạng ngày hiển thị: dd/mm/yy (ví dụ: 01/05/26)