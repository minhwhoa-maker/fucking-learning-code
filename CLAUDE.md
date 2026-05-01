# Fleet App Context

## Stack
- Vanilla HTML/CSS/JS + Supabase + Vercel
- Supabase project: icwmtqfpbefntfxboofr
- Repo: minhwhoa-maker/fucking-learning-code
- Live URL: https://fucking-learning-code.vercel.app

## Files
- bai10.html: Login page (Google OAuth + role redirect)
- owner-dashboard.html: Owner dashboard (trips, stats, filter month, realtime)
- driver-page.html: Driver trip input (nhập chuyến, tạm ứng, hoàn ứng)
- drivers.html: Driver management (danh sách, lương, tạm ứng tháng, export Excel/PDF)
- vehicles.html: Vehicle management (danh sách xe, trạng thái)
- CONTEXT.md: This file

## Database tables
- users (id, email, full_name, sdt, role)
- trips (id, ngay, tuyen_duong, doanh_thu, chi_phi, luong_chuyen, tam_ung, hoan_ung, tai_xe_id, ghi_chu)
- tam_ung_thang (id, tai_xe_id, thang, so_tien, ghi_chu)
- xe (id, bien_so, loai_xe, nam_sx, trang_thai, tai_xe_id)
- bao_duong (id, xe_id, ngay, loai, mo_ta, chi_phi) — đang build

## Supabase anon key
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imljd210cWZwYmVmbnRmeGJvb2ZyIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzY5Mzg3NzgsImV4cCI6MjA5MjUxNDc3OH0.N1gsPt4eZav2LL2XDttqlsAB06b1UzXb4bFTMi3K8NM

## Current status
- Bài 22: Bảo dưỡng/hỏng hóc xe — đang build
- Table bao_duong cần tạo trong Supabase

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
- Bài 22: Bảo dưỡng xe (đang làm)

## Notes
- RLS disabled trên tất cả tables (cần fix sau)
- Anon key đừng share public
- Mỗi lần Claude Code mới → paste file này vào đầu