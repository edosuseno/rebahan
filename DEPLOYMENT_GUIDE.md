# Panduan Deployment AE PRO Pusat Drama

Dokumen ini berisi langkah-langkah untuk meng-upload dan menjalankan project AE PRO (Frontend & Backend) ke server production.

---

## 1. Persiapan Environment
Pastikan Anda telah mengupdate file `.env` di masing-masing folder sebelum melakukan proses build.

### **Backend (.env)**
Lokasi: `/backend/.env`
```env
PORT=5000
NODE_ENV=production
# Tambahkan variabel lain yang diperlukan
```

### **Frontend (.env.production)**
Lokasi: `/frontend/.env.production`
```env
# Arahkan ke domain/IP backend Anda
NEXT_PUBLIC_API_BASE_URL=https://api.domain-anda.com
```

---

## 2. Opsi A: Deployment via VPS (Sangat Disarankan)
Metode ini menggunakan Ubuntu, Nginx, dan PM2 untuk stabilitas maksimal.

### **Langkah 1: Setup Backend**
1. Masuk ke direktori backend: `cd backend`
2. Install dependencies: `npm install`
3. Jalankan aplikasi menggunakan PM2:
   ```bash
   npm install -g pm2
   pm2 start src/index.js --name "aepro-backend"
   ```

### **Langkah 2: Setup Frontend (Next.js)**
1. Masuk ke direktori frontend: `cd frontend`
2. Install dependencies: `npm install`
3. Build project untuk production:
   ```bash
   npm run build
   ```
4. Jalankan hasil build menggunakan PM2:
   ```bash
   pm2 start npm --name "aepro-frontend" -- start
   ```

### **Langkah 3: Konfigurasi Nginx**
Gunakan Nginx sebagai reverse proxy. Contoh config sederhana:
```nginx
server {
    listen 80;
    server_name domain-anda.com;

    location / {
        proxy_pass http://localhost:3000; # Port Frontend
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location /api {
        proxy_pass http://localhost:5000; # Port Backend
        proxy_set_header Host $host;
    }
}
```

---

## 3. Opsi B: Deployment via Shared Hosting (cPanel)
Gunakan fitur **"Setup Node.js App"** di cPanel.

### **Backend**
1. Create Application di menu Node.js cPanel.
2. **Application root**: folder `backend`.
3. **Application startup file**: `src/index.js`.
4. Klik **Run JS Install**.

### **Frontend**
1. Lakukan `npm run build` di komputer lokal Anda.
2. Compress folder `.next`, `public`, `package.json`, dan `node_modules`.
3. Upload dan extract di folder hosting.
4. Setup Node.js App di cPanel dengan **Application startup file** mengarah ke script start Next.js.

---

## 4. Opsi C: Cloud Deployment (Vercel & Railway)
Metode paling mudah jika Anda menggunakan GitHub.

1. **Frontend**: Hubungkan repository GitHub folder `frontend` ke [Vercel](https://vercel.com). Vercel akan mendeteksi project Next.js secara otomatis.
2. **Backend**: Hubungkan folder `backend` ke [Railway.app](https://railway.app) atau [Render.com](https://render.com).

---

## Tips Maintenance
- **Cek Status**: `pm2 status`
- **Restart Aplikasi**: `pm2 restart all`
- **Lihat Log Error**: `pm2 logs`

*Dokumen ini dibuat pada: 3 Februari 2026*
