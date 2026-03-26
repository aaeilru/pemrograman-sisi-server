# Simple LMS

Setup environment development Django dengan Docker Compose, PostgreSQL sebagai database, dan Redis sebagai cache.

## 🚀 Features

- **Django 4.2** — Web framework Python untuk backend LMS
- **PostgreSQL 15** — Database relasional dengan health check otomatis
- **Redis 7** — In-memory cache untuk performa aplikasi
- **Gunicorn** — Production-grade WSGI server
- **Docker Compose** — Multi-container orchestration untuk development
- **dj-database-url** — Parse `DATABASE_URL` otomatis ke konfigurasi Django

## 🏗️ Architecture

```
simple-lms/
├── docker-compose.yml        # Konfigurasi multi-container stack
├── Dockerfile                # Build image Django app
├── requirements.txt          # Python dependencies
├── manage.py                 # Django CLI
├── .env                      # Environment variables (tidak di-commit)
├── .env.example              # Template environment variables
├── .gitignore
├── config/
│   ├── settings.py           # Django settings + DATABASE_URL config
│   ├── urls.py               # URL routing
│   └── wsgi.py               # WSGI entry point
├── README.md
└── screenshots/
    ├── django-welcome.png
    └── docker-ps.png
```

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Web Framework | Django 4.2 |
| WSGI Server | Gunicorn |
| Database | PostgreSQL 15 Alpine |
| Cache | Redis 7 Alpine |
| Language | Python 3.11 Slim |
| Orchestration | Docker Compose |
| Network | Docker Bridge Network (`lms-network`) |
| Storage | Docker Named Volumes |

## 📦 Getting Started

### Prerequisites

- Docker Desktop (sudah terinstall dan running)
- Docker Compose (sudah include di Docker Desktop)

### Quick Start

```bash
# Clone repository
git clone https://github.com/aaeilru/pemrograman-sisi-server.git
cd pemrograman-sisi-server/simple-lms

# Copy environment file
cp .env.example .env

# Build dan jalankan semua services
docker-compose up -d --build

# Jalankan migrasi database
docker-compose exec app python manage.py migrate

# Cek status container
docker-compose ps
```

Akses aplikasi:
- **Django:** http://localhost:8000
- **Django Admin:** http://localhost:8000/admin

### Membuat Superuser (Admin)

```bash
docker-compose exec app python manage.py createsuperuser
```

### Menghentikan Stack

```bash
# Stop semua container (data tetap tersimpan)
docker-compose down

# Stop dan hapus semua volume (data hilang!)
docker-compose down -v
```

## 🔒 Environment Variables

| Variable | Deskripsi | Contoh |
|----------|-----------|--------|
| `DEBUG` | Mode debug Django | `True` |
| `SECRET_KEY` | Django secret key | *(ganti di production!)* |
| `DATABASE_URL` | URL koneksi PostgreSQL | `postgres://postgres:postgres@database:5432/lms_db` |
| `REDIS_URL` | URL koneksi Redis | `redis://redis:6379/0` |

> **Catatan:** Kata `database` dan `redis` pada URL adalah **nama service** di `docker-compose.yml`. Docker secara otomatis meresolve nama service ke IP internal container melalui `lms-network`.

## 🧪 Testing & Verifikasi

```bash
# Cek semua container running
docker ps

# Cek logs semua service
docker-compose logs -f

# Cek logs service tertentu
docker-compose logs -f app
docker-compose logs -f database

# Masuk ke container Django
docker-compose exec app bash

# Masuk ke PostgreSQL shell
docker-compose exec database psql -U postgres -d lms_db

# Jalankan migrasi
docker-compose exec app python manage.py migrate
```

## 📸 Screenshots

### Django Welcome Page
![Django Welcome Page](./screenshots/django-welcome.png)

### Docker PS — Containers Running
![Docker PS](./screenshots/docker-ps.png)

## 🔍 Troubleshooting

| Masalah | Solusi |
|---------|--------|
| PostgreSQL tidak start | Cek `POSTGRES_USER` dan `POSTGRES_PASSWORD` di `docker-compose.yml` |
| Django tidak connect ke DB | Pastikan `DATABASE_URL` menggunakan hostname `database` (nama service) |
| Port 8000 sudah dipakai | Ubah ke `"8080:8000"` di `docker-compose.yml` |
| Container app langsung exit | Jalankan `docker-compose logs app` untuk debug |
| Migrasi gagal | Tunggu PostgreSQL `healthy`, lalu jalankan migrate ulang |
| `collectstatic` error saat build | Pastikan `ENV DJANGO_SETTINGS_MODULE=config.settings` ada di Dockerfile |

---

*Tugas Mata Kuliah Pemrograman Sisi Server — Universitas Dian Nuswantoro*  