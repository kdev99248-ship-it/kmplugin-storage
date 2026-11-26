# Kho plugin KMedia (kho chính thức)

Kho chỉ là **danh sách**: mỗi plugin một file `plugins/<id>.json` có mô tả ngắn và link tải. File `.kmplug` nằm ở chỗ tác giả tự lo (GitHub / Codeberg Releases, trang riêng). CI của repo này tải từng gói, kiểm, rồi xuất `index.json` đã ký lên GitHub Pages; app KMedia đọc danh sách đó. Đặc tả: `plugin-sdk/spec.md` §11.

Kho chính thức nhận **nguồn thư viện, mở rộng trình đọc và plugin thư viện**; **không nhận nguồn nội dung** (truyện online). Nguồn nội dung nằm ở kho do người khác lập.

## Gửi plugin

1. Đóng gói và ký plugin (`kmplug sign`), đăng file `.kmplug` ở chỗ của bạn, link `https` cố định cho từng phiên bản (không thay file sau khi đã gửi).
2. Mở Pull Request thêm hoặc sửa **một** file `plugins/<id>.json`:

```json
{
  "id": "io.github.alice.tratu",
  "homepage": "https://github.com/alice/tratu",
  "versions": [
    {"url": "https://github.com/alice/tratu/releases/download/v1.1.0/tratu-1.1.0.kmplug", "released": "2026-12-01"},
    {"url": "https://github.com/alice/tratu/releases/download/v1.0.0/tratu-1.0.0.kmplug", "released": "2026-11-25"}
  ]
}
```

Chỉ ghi `id`, link và ngày; tên, loại, tác giả, mô tả, quyền, phụ thuộc, `sha256`, `size`, khoá ký đều do CI lấy từ chính gói. Tuỳ chọn: `icon` (URL `https`), `languages`, `nsfw`.

CI từ chối PR nếu: không tải được gói; gói sai đặc tả hoặc chưa ký; `id` khác tên file; các phiên bản ký bằng khoá khác nhau; là nguồn nội dung; hoặc **file sau một link đã đăng bị thay** (mỗi link được ghim bằng `sha256` từ lần đăng trước).

## Thu hồi

Thêm `"revoked": {"reason": "…"}` vào cả plugin hoặc vào một phiên bản trong `versions`. Lần kiểm tra sau, app tắt bản đã cài bị thu hồi và hiện lý do.

## Cho người giữ kho (làm một lần)

1. Tạo khoá ký kho: `kmplug keygen kmedia-repo.key`. Giữ file này bí mật, sao lưu ở chỗ an toàn; mất khoá thì app không nhận danh sách mới nữa.
2. Repo settings → Secrets: `KMPLUG_REPO_KEY` = nội dung file khoá. Variables: `KMPLUG_URL` = link tải bản `kmplug` cho Linux x86_64 (build từ mã KMedia: `cargo build --release -p bigtext-plugin --features cli --bin kmplug`). Pages: nguồn "GitHub Actions".
3. Sửa `repo.json` (`homepage`).
4. Trong app, đặt `OfficialRepo.URL` (`https://<chủ>.github.io/<repo>/index.json`) và `OfficialRepo.FINGERPRINT` (in ra ở bước 1) trong `android/core/data/.../plugin/PluginCatalog.kt`.

Chạy thử trên máy: `kmplug index <thư mục repo này> --official [--key kmedia-repo.key]` ghi `site/index.json` (+ `.sig`).
