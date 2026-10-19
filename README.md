# TBStore catalog — boxgovap

Kho ứng dụng cho **TBStore** (Android Box). Repo này chứa *danh sách* app và icon;
các file `.apk` nằm ở tab **Releases** (không commit APK vào Git).

## Cấu trúc

```
catalog.json      danh sách app — file TBStore đọc
categories.json   danh mục hiển thị trên chip lọc
icons/            icon PNG 512×512, tên file = id app (tbstore.png, vlc.png…)
banners/          banner PNG 320×180 cho app nổi bật
```

## URL để dán vào TBStore

```
https://raw.githubusercontent.com/boxgovap/tbstore-catalog/main/catalog.json
```

(hoặc bật GitHub Pages rồi dùng
`https://boxgovap.github.io/tbstore-catalog/catalog.json`)

Trên box: mở **TBStore** → ⚙️ **Cài đặt** → dán URL → **💾 Lưu và tải lại**.

## Thêm app mới

```powershell
Set-Location E:\Du_Lieu\AI\AppAPK\TBStore
$env:ANDROID_HOME = "$env:LOCALAPPDATA\Android\Sdk"

.\scripts\publish-app.ps1 -Apk "D:\apk\ten-app.apk" `
  -Repo boxgovap/tbstore-catalog -Id ten-app -Category media `
  -ShortDescription "Mô tả ngắn" `
  -IconUrl "https://raw.githubusercontent.com/boxgovap/tbstore-catalog/main/icons/ten-app.png" `
  -Catalog "E:\Du_Lieu\AI\AppAPK\tbstore-catalog\catalog.json" -Release
```

Script tự đọc `packageName` / `versionName` / `versionCode` / `minSdk` bằng `aapt`,
tự tính `sizeBytes` + `sha256`, rồi cập nhật `catalog.json` và upload APK lên Release.

Sau đó:

```powershell
Set-Location E:\Du_Lieu\AI\AppAPK\tbstore-catalog
git add . ; git commit -m "add ten-app" ; git push
```

## Trường bắt buộc mỗi app

| Trường | Kiểu | Ghi chú |
|---|---|---|
| `packageName` | string | phải khớp APK |
| `name` | string | tên hiển thị |
| `versionCode` | int | **tăng dần mỗi bản** — TBStore so số này |
| `versionName` | string | chỉ để hiển thị |
| `apkUrl` | string | link Release asset |
| `sha256` | string | TBStore xác thực APK bằng mã này |
| `sizeBytes` | int | kiểm tra dung lượng trống trước khi tải |
| `categoryId` | string | khớp `id` trong `categories.json` |

Tuỳ chọn: `shortDescription`, `description`, `developer`, `iconUrl`, `bannerUrl`,
`changelog`, `screenshots[]`, `minSdk`, `releaseDate`, `featured`, `enabled`,
`variants[]` (chọn APK theo ABI).

Đặt `"enabled": false` để tạm ẩn một app mà không phải xoá entry.

## Lưu ý

- GitHub web chỉ cho upload **25 MB/file**; `git push` chặn ở **100 MB**;
  Release cho tới **2 GB** mỗi asset → luôn dùng **Releases** cho APK.
- APK cập nhật phải cùng chữ ký với bản cũ, nếu không Android sẽ từ chối cài đè.
- `sha256` sai một ký tự là TBStore xoá file và báo lỗi — cứ chạy script, đừng gõ tay.
