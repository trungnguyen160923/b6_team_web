# B6 Team Website

Trang web chính thức (Developer Website) của B6 Team Studio dành cho:
- **Google Play Store**: Khai báo Developer Website và liên kết Privacy Policies.
- **Google Search Console**: Index trang web với đầy đủ nội dung, sitemap và robots.txt.
- **Google AdMob / AppLovin**: Phục vụ file `app-ads.txt`.

---

## 📁 Cấu trúc thư mục

```text
b6_team_web/
├── index.html        # Trang chủ Landing Page giới thiệu game & B6 Team
├── app-ads.txt       # File khai báo AdMob publisher ID
├── robots.txt        # Cho phép Googlebot thu thập dữ liệu
├── sitemap.xml       # Sơ đồ trang web để submit lên Search Console
└── README.md
```

---

## 🚀 Các cách Deploy lên https://b6-team.site

### Cách 1: Phục vụ trực tiếp qua Nginx trên VPS (Khuyên dùng)
Nếu VPS của bạn đang dùng Nginx làm reverse proxy cho domain `b6-team.site`:
1. Copy toàn bộ các file trong thư mục này vào VPS tại thư mục: `/var/www/b6_team_web/`
2. Cấu hình Nginx:
   ```nginx
   server {
       listen 80;
       listen 443 ssl;
       server_name b6-team.site;

       root /var/www/b6_team_web;
       index index.html;

       # Phục vụ trang chủ, app-ads.txt, sitemap.xml, robots.txt
       location / {
           try_files $uri $uri/ =404;
       }

       # Giữ nguyên các API đang chạy
       location /api/ {
           proxy_pass http://127.0.0.1:8000;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
       }
   }
   ```
3. Chạy `sudo nginx -t && sudo systemctl reload nginx`.

---

### Cách 2: Tích hợp trực tiếp vào FastAPI
Nếu máy chủ của bạn chạy trực tiếp Python FastAPI:
```python
from fastapi import FastAPI
from fastapi.responses import FileResponse, PlainTextResponse

app = FastAPI()

@app.get("/")
def home():
    return FileResponse("index.html")

@app.get("/app-ads.txt", response_class=PlainTextResponse)
def app_ads():
    with open("app-ads.txt", "r") as f:
        return f.read()

@app.get("/robots.txt", response_class=PlainTextResponse)
def robots():
    with open("robots.txt", "r") as f:
        return f.read()

@app.get("/sitemap.xml")
def sitemap():
    return FileResponse("sitemap.xml", media_type="application/xml")
```

---

## 🔍 Bước tiếp theo sau khi deploy
1. Mở [Google Search Console](https://search.google.com/search-console).
2. Vào mục **Sitemaps** -> Thêm `sitemap.xml`.
3. Nhập URL `https://b6-team.site/` vào thanh kiểm tra -> Bấm **Test Live URL** -> Bấm **Request Indexing**.
4. Vào mục cảnh báo -> Bấm **Validate Fix**.
