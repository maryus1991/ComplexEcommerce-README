# ComplexEcommerce

> A powerful, multi-vendor e-commerce platform built with Django 5.2 — designed for the Iranian market with full support for local payment gateways, SMS, shipping providers, and Jalali calendar.

![Django](https://img.shields.io/badge/Django-5.2-092E20?style=for-the-badge&logo=django&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-7-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![Celery](https://img.shields.io/badge/Celery-5-37814A?style=for-the-badge&logo=celery&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?style=for-the-badge&logo=docker&logoColor=white)

---

## ✨ Overview

**ComplexEcommerce** is a full-featured, production-ready multi-vendor marketplace. It allows multiple stores to sell products on a single platform while providing a rich shopping experience for end users.

The project is optimized for the Iranian ecosystem and includes deep integrations with:

- **Zarinpal** & **Zipal** payment gateways
- **Kavenegar** for OTP and SMS
- **Tipax** & **Postex** for shipping cost calculation
- Jalali (Persian) calendar
- Iranian phone number validation

---

## 🚀 Key Features

### Core Commerce
- Multi-vendor / multi-store architecture
- Hierarchical product categories & brands
- Product variants (parent/child products)
- Advanced pricing (regular price, colleague price, percentage discounts)
- Product galleries, specifications, tags & features
- Stock management with availability notifications
- Product comments + like/dislike system
- User favorites / wishlist

### Orders & Checkout
- Complex order calculation engine (discounts, taxes, official invoices, extra store fees)
- Multiple shipping methods (Tipax, Postex, VIP Post, In-city, Freight, Physical)
- Real-time shipping price calculation with caching
- Official invoice support with PDF generation
- Order cancellation & return flows
- Discount codes (global, product, store, or user-specific)

### User System
- Phone-number based authentication (OTP via Kavenegar)
- Email verification
- Colleague / B2B users with special pricing
- Legal person (company) profiles
- User dashboard (addresses, tickets, orders history)
- Store admin role

### Content & SEO
- Full blog system with CKEditor
- Dynamic site settings
- Automatic sitemaps (products, blog, stores, pages)
- `robots.txt` generation
- HTML minification & static file compression
- WebP image optimization via Easy Thumbnails

### Admin & Store Panels
- Dedicated Admin Panel
- Dedicated Store Panel (for vendors)
- Audit logging
- Database backups
- Ticket system

### Performance & Background Tasks
- Redis caching (with optional django-cachalot)
- Celery + Redis for async tasks (OTP, SMS, Email, cleanup jobs)
- Multiple Celery queues (`otp`, `sms`, `email`, `details`, `clean`)
- Session stored in cached database

### Security
- Google reCAPTCHA v3
- HSTS, Secure Cookies, XSS & Content-Type protection
- CSRF protection with trusted origins
- Random token protection for admin & store panels in production
- Content Security Policy ready

---

## 🛠 Tech Stack

| Layer              | Technology                          |
|--------------------|-------------------------------------|
| Backend            | Django 5.2                          |
| Database           | PostgreSQL                          |
| Cache / Broker     | Redis                               |
| Task Queue         | Celery + django-celery-beat         |
| Authentication     | Custom User (Phone + OTP)           |
| Payment            | Zarinpal / Zipal                    |
| SMS                | Kavenegar                           |
| Shipping           | Tipax + Postex                      |
| Rich Text          | CKEditor                            |
| Image Processing   | Easy Thumbnails (WebP)              |
| Date               | django-jalali-date                  |
| Frontend Assets    | django-compressor + HTMLMin         |
| Containerization  | Docker                              |

---

## 📁 Project Structure

```
ComplexEcommerce/
├── admin_panel/          # Super admin panel
├── blog/                 # Blog system
├── config/               # Settings, Celery, Middleware, URLs
├── dashboard/            # User dashboard (tickets, addresses...)
├── guarantee/            # Guarantee / warranty system
├── nginx/                # Nginx configuration
├── orders/               # Order & payment logic
├── product/              # Products, categories, brands, comments
├── redis/                # Redis related files
├── sitesetting/          # Site-wide settings + homepage
├── static/               # Static files
├── stores/               # Multi-vendor stores + store panel
├── templates/            # Global templates
├── user/                 # Custom user model + auth
├── utils/                # Shared utilities (OTP, email, price calc...)
├── Dockerfile
├── manage.py
├── req.txt               # Production dependencies
└── dev-req.txt           # Development tools (debug-toolbar, silk)
```

---

## ⚙️ Installation & Setup

### 1. Clone the repository

```bash
git clone https://github.com/maryus1991/ComplexEcommerce.git
cd ComplexEcommerce
```

### 2. Create virtual environment

```bash
python -m venv venv
source venv/bin/activate   # Linux / macOS
# or
venv\Scripts\activate      # Windows
```

### 3. Install dependencies

```bash
pip install -r req.txt
# For development:
pip install -r dev-req.txt
```

### 4. Environment variables

Create a `.envs` file in the project root:

```env
DJANGO_SECRET_KEY=your-very-secret-key
DJANGO_DEBUG=1
ALLOWED_HOSTS=localhost,127.0.0.1

POSTGRES_ENGIN=django.db.backends.postgresql
POSTGRES_DB=complexecommerce
POSTGRES_USER=postgres
POSTGRES_PASSWORD=yourpassword
POSTGRES_HOST=localhost
POSTGRES_PORT=5432

# Google reCAPTCHA
GOOGLE_RECAPTCHA_SITE_KEY=...
GOOGLE_RECAPTCHA_SECRET_KEY=...

# Kavenegar
KAVEH_NEGAR_OTP=your-api-key

# Zarinpal
MERCHANT_ID=...
ZARINPAL_SEND_DATA_URL=...
ZARINPAL_REDIRECT_USER_TO_PAYMENT_PAGE=...
ZARINPAL_VERIFY_PAYMENT=...

# Tipax
TIPAX_USERNAME=...
TIPAX_PASSWORD=...
TIPAX_API_KEY=...

# Postex
POSTEX_API_KEY=...
```

### 5. Database setup

```bash
python manage.py migrate
python manage.py createsuperuser
```

### 6. Run the development server

```bash
python manage.py runserver
```

### 7. Celery (optional but recommended)

```bash
celery -A config worker -l info -Q otp,sms,email,details,clean
celery -A config beat -l info
```

---

## 🐳 Docker Support

A `Dockerfile` is included. You can build and run the project with Docker (make sure Redis and PostgreSQL are available).

```bash
docker build -t complex-ecommerce .
docker run -p 8000:8000 --env-file .envs complex-ecommerce
```

---

## 📌 Important Notes

- In **production**, admin and store panel URLs are protected with long random tokens.
- The project uses `Asia/Tehran` timezone and full Jalali date support.
- Shipping prices are calculated on-the-fly and cached for 3 days.
- Official invoices and extra taxes can be configured per store.
- Colleague users can have special wholesale pricing.

---

## 🤝 Contributing

Contributions, issues and feature requests are welcome!  
Feel free to open an issue or submit a pull request.

---

## 📄 License

This project is currently private / proprietary.  
Contact the author for licensing details.

---

## 👨‍💻 Author

**maryus**  
GitHub: [maryus1991](https://github.com/maryus1991)

---

**Happy Selling! 🛒**
