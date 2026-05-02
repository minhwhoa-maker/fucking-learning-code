# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Fleet management app cho công ty vận tải Ea Kar — owner theo dõi chuyến/doanh thu/lương, driver nhập chuyến và upload ảnh hóa đơn. Đây là dự án học code (`bai*`) tăng dần, các file `bai1-9.html` là bài tập cũ, app thực tế chạy trên `bai10.html` + 4 trang admin/driver.

- Stack: Vanilla HTML/CSS/JS + Supabase (Postgres + Auth + Storage + Realtime) + Vercel
- Live: https://fucking-learning-code.vercel.app
- Supabase project ref: `icwmtqfpbefntfxboofr`
- Anon key (public, đã có trong `shared.js`):
  ```
  eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imljd210cWZwYmVmbnRmeGJvb2ZyIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzY5Mzg3NzgsImV4cCI6MjA5MjUxNDc3OH0.N1gsPt4eZav2LL2XDttqlsAB06b1UzXb4bFTMi3K8NM
  ```

## Workflow

Không có build step, không có test runner, không có lint. Quy trình:

- **Edit**: sửa file `.html`/`.css`/`.js` trực tiếp.
- **Preview local**: mở file qua `file://` (đa số chạy được), hoặc `python -m http.server` để tránh lỗi service worker / relative path.
- **Deploy**: `git push origin main` → Vercel auto-deploy.
- **DB schema changes**: vào Supabase dashboard project `icwmtqfpbefntfxboofr` chỉnh tay (SQL editor hoặc Table editor).

## Architecture

### Page roles
- `bai10.html` — landing page + Google OAuth + role redirect. Có `<style>` block riêng (~220 dòng) cho hero/stats/features layout (KHÔNG dùng `.card` chuẩn).
- `owner-dashboard.html` — owner xem báo cáo trips, filter tháng, realtime subscribe `trips`. Header có nav đến driver/vehicles.
- `driver-page.html` — driver nhập chuyến, upload ảnh hóa đơn (camera + gallery), xem chuyến của mình.
- `driver.html` — owner quản lý tài xế, tính lương theo tháng, export Excel/PDF.
- `vehicles.html` — owner quản lý xe + bảo dưỡng inline.
- `style.css` — design system shared, dùng CSS variables.
- `shared.js` — JS utilities shared (xem dưới).
- `sw.js` + `manifest.json` — PWA, chỉ register từ `bai10.html`.

### shared.js (BẮT BUỘC dùng cho mọi page mới)
```
createSb()              → tạo Supabase client với URL+anon key built-in
formatMoney(n)          → "1.234.567 đ" (vi-VN locale + đ ký tự)
formatDate("YYYY-MM-DD") → "DD/MM/YY"
getUserRole(sb, email)  → role string hoặc null
getUserProfile(sb, email) → { id, role } hoặc null
requireRole(sb, role)   → đảm bảo session + role khớp; redirect bai10 nếu không.
                          Trả { user, profile } hoặc null.
setupLogoutListener(sb) → tự redirect bai10 khi logout từ tab khác.
```

Mỗi page admin (driver/vehicles/owner-dashboard) bắt đầu với:
```js
const sb = createSb()
async function initPage() {
    const auth = await requireRole(sb, 'owner')  // hoặc 'driver'
    if (!auth) return
    // ... load data
}
setupLogoutListener(sb)
initPage()
```

### Auth flow & 2 ID schemes (gotcha quan trọng)
`users.id` có thể có 2 origin khác nhau:
- (a) **Auth UUID**: khi user signup qua `bai10.checkUserRole` (Google login lần đầu, chưa có row) → INSERT với `id: session.user.id`.
- (b) **DB-generated UUID**: khi owner tạo trước qua `driver.html addDriver` (không set id, để DB auto-gen).

Khi user case (b) login lần đầu, bai10 thấy email đã có → skip insert → `users.id` ≠ Auth UUID. Vì vậy **mọi reference trong app phải dùng `users.id` (qua `currentProfileId`), KHÔNG dùng `currentUser.id` (Auth UUID)**:
- `trips.tai_xe_id` → `currentProfileId`
- Storage path receipts → `${currentProfileId}/{timestamp}.{ext}`

`currentUser.id` (Auth UUID) chỉ dùng cho session check, không leak vào DB.

### CSS conventions
- CSS variables ở `:root` của `style.css`: `--primary #1565c0`, `--danger #e74c3c`, `--success #27ae60`, `--warning #e67e22`, `--bg #f0f2f5`, `--shadow`, `--radius 12px`, `--radius-sm 8px`.
- Button classes: `.btn` (xanh primary), `.btn-danger/.btn-success/.btn-warning/.btn-purple/.btn-gray/.btn-logout/.btn-full/.btn-sm`. **Không dùng inline `style="background:..."`** — đã có class.
- Stat values trong owner-dashboard: `.stat-value.green/red/blue/orange`.
- Bảng nhiều cột bọc trong `<div class="table-scroll wide">` để mobile scroll ngang.
- Status messages: dùng class `.message.success/.error/.empty` (style.css), JS gán qua `className`.
- All asset links dùng **relative path** (`manifest.json`, `style.css`, `sw.js`, `shared.js`) — không có leading `/`.

### Realtime subscriptions
`owner-dashboard.html` subscribe channel `trips-changes` cho table `trips`. Channel được lưu trong `tripsChannel` và cleanup ở `beforeunload` qua `sb.removeChannel(tripsChannel)`.

## Database

```
users          (id, email, full_name, sdt, role)              -- role: 'owner' | 'driver'
trips          (id, ngay, tuyen_duong, doanh_thu, chi_phi, luong_chuyen, tam_ung, hoan_ung, tai_xe_id, ghi_chu, anh_hoa_don)
tam_ung_thang  (id, tai_xe_id, thang, so_tien, ghi_chu)       -- thang format: 'YYYY-MM'
xe             (id, bien_so, loai_xe, nam_sx, trang_thai, tai_xe_id)  -- trang_thai: 'hoat_dong' | 'bao_duong' | 'tam_nghi'
bao_duong      (id, xe_id, ngay, loai, mo_ta, chi_phi, created_at)    -- loai: 'hong_hoc' | 'linh_kien' | 'lop_xe' | 'dinh_ky'
```

- `tai_xe_id` luôn = `users.id` (không phải Auth UUID).
- `ngay` format `YYYY-MM-DD`, hiển thị qua `formatDate()` thành `DD/MM/YY`.

## Storage

- Bucket: `receipts` (cần Public access để `getPublicUrl()` hoạt động).
- Path format: `{users.id}/{timestamp}.{ext}` — extension đã sanitize regex.

## Notes / Gotchas

- **RLS disabled** trên tất cả tables. Khi bật RLS, các chỗ sau sẽ break:
  - `bai10.loadStats()` — query `trips`/`users` công khai để hiển thị landing stats.
  - Page admin/driver sẽ cần policy "user đọc được row của mình" + "owner đọc được tất cả".
- **`bai10.checkUserRole`** là duy nhất chỗ INSERT vào `users` từ Google OAuth (signup-on-login). `shared.getUserRole/getUserProfile` chỉ select.
- **`bai10.formatStatNumber`** (local) ≠ `shared.formatMoney`: bai10 hiển thị dạng rút gọn `1.2B`/`345M`/`12K`, các page khác dùng full `1.234.567 đ`.
- **Date format hiển thị**: luôn `dd/mm/yy` (2 chữ số năm).
- **Currency**: luôn `đ` (chữ thường), KHÔNG dùng `₫` unicode.
- **Google OAuth `redirectTo`**: dùng `window.location.origin + '/bai10.html'` để hoạt động cả local và production.

## Recent changes log

Các bài học gần nhất (chi tiết git log):

- Bài 29 (2026-05-03): Tạo `shared.js`, fix lệch ID Auth/users, đồng bộ format tiền tệ, thêm nav `vehicles.html` ở header owner-dashboard, `setupLogoutListener` cho mọi admin page.
- Bài 28: Redesign `bai10.html` thành landing mobile-first (hero + stats public + features + CTA).
- Bài 27: Security hardening — chuyển từ `innerHTML` template literal sang `createElement`/`textContent` (chống XSS), validation `Number.isFinite`, error handling toàn diện, `.table-scroll` wrapper cho mobile.
- Bài 26: Tách shared `style.css`, xóa `<style>` blocks khỏi 4 page admin/driver.
- Bài 25 trở về trước: xem CONTEXT.md cũ hoặc `git log`.
