# 🛒 DigiShop — Digital Account Shop

Website bán tài khoản số hiện đại, full-stack với Next.js 15 + TypeScript + TailwindCSS + PostgreSQL.

---

## 📁 Cấu trúc thư mục

```
digital-shop/
├── prisma/
│   └── schema.prisma          # Database schema
├── scripts/
│   └── seed.ts                # Dữ liệu mẫu
├── src/
│   ├── app/
│   │   ├── page.tsx           # Trang chủ
│   │   ├── layout.tsx         # Root layout
│   │   ├── globals.css        # Global styles
│   │   ├── cart/              # Giỏ hàng
│   │   ├── checkout/          # Thanh toán
│   │   ├── invoice/[id]/      # Hóa đơn
│   │   ├── orders/lookup/     # Tra cứu đơn hàng
│   │   ├── admin/             # Trang quản trị
│   │   │   ├── page.tsx       # Dashboard
│   │   │   ├── login/         # Đăng nhập admin
│   │   │   ├── products/      # Quản lý sản phẩm
│   │   │   ├── orders/        # Quản lý đơn hàng
│   │   │   ├── customers/     # Quản lý khách hàng
│   │   │   └── accounts/      # Kho tài khoản
│   │   └── api/
│   │       ├── auth/login/    # API đăng nhập
│   │       ├── products/      # API sản phẩm
│   │       ├── orders/        # API đơn hàng
│   │       └── admin/         # API admin (stats, accounts)
│   ├── components/
│   │   ├── layout/            # Header, Footer, ThemeProvider
│   │   └── shop/              # ProductCard, ProductGrid, HeroSection
│   ├── lib/
│   │   ├── prisma.ts          # Prisma client
│   │   ├── auth.ts            # JWT utilities
│   │   └── utils.ts           # Helper functions
│   ├── store/
│   │   └── cartStore.ts       # Zustand cart state
│   └── types/
│       └── index.ts           # TypeScript types
├── .env.example
├── package.json
├── tailwind.config.js
└── tsconfig.json
```

---

## 🚀 Cài đặt & Chạy

### Yêu cầu
- Node.js 18+
- PostgreSQL 14+
- npm hoặc yarn

### Bước 1: Clone và cài dependencies

```bash
git clone <repo-url>
cd digital-shop
npm install
```

### Bước 2: Cấu hình môi trường

```bash
cp .env.example .env
```

Chỉnh sửa `.env`:
```env
DATABASE_URL="postgresql://user:password@localhost:5432/digital_shop"
JWT_SECRET="your-random-secret-key"
ADMIN_EMAIL="admin@yourdomain.com"
ADMIN_PASSWORD="YourSecurePassword123!"

# Thông tin ngân hàng (VietQR)
NEXT_PUBLIC_BANK_NAME="MB Bank"
NEXT_PUBLIC_BANK_ACCOUNT="0901234567"
NEXT_PUBLIC_BANK_HOLDER="TEN CUA BAN"
NEXT_PUBLIC_VIETQR_BANK_ID="MB"
NEXT_PUBLIC_VIETQR_ACCOUNT_NO="0901234567"
NEXT_PUBLIC_VIETQR_ACCOUNT_NAME="TEN CUA BAN"
```

### Bước 3: Tạo database

```bash
# Tạo database PostgreSQL
createdb digital_shop

# Chạy migration
npm run db:push

# Tạo dữ liệu mẫu
npm run db:seed
```

### Bước 4: Chạy dev server

```bash
npm run dev
```

Mở trình duyệt: http://localhost:3000

---

## 🔐 Tài khoản Admin

Sau khi seed:
- **URL**: http://localhost:3000/admin/login
- **Email**: admin@digitalshop.vn (hoặc giá trị ADMIN_EMAIL)
- **Password**: Admin@123456 (hoặc giá trị ADMIN_PASSWORD)

---

## 📡 API Documentation

### Authentication
```
POST /api/auth/login
Body: { email, password }
Response: { token, user }
```

### Products
```
GET    /api/products              - Danh sách sản phẩm
POST   /api/products              - Thêm sản phẩm [Admin]
GET    /api/products/:id          - Chi tiết sản phẩm
PUT    /api/products/:id          - Cập nhật sản phẩm [Admin]
DELETE /api/products/:id          - Xoá sản phẩm [Admin]
```

### Orders
```
POST   /api/orders                - Tạo đơn hàng
GET    /api/orders                - Danh sách đơn hàng [Admin]
GET    /api/orders/:id            - Chi tiết đơn hàng
PATCH  /api/orders/:id            - Cập nhật trạng thái [Admin]
  Body actions: mark_paid | deliver | CANCELLED
GET    /api/orders/lookup?q=...   - Tra cứu đơn hàng (public)
```

### Admin
```
GET    /api/admin/stats           - Thống kê dashboard [Admin]
GET    /api/admin/accounts        - Danh sách tài khoản kho [Admin]
POST   /api/admin/accounts        - Thêm tài khoản vào kho [Admin]
```

---

## 🔄 Quy trình hoạt động

```
Khách hàng                      Admin
    │                             │
    ├─ Xem sản phẩm               │
    ├─ Thêm vào giỏ hàng         │
    ├─ Nhập thông tin checkout    │
    ├─ Xem QR Code thanh toán     │
    ├─ Chuyển khoản               │
    ├─ Bấm "Đã chuyển khoản"      │
    │  ← Tạo đơn (PENDING) ─────→ Nhận thông báo
    │                             ├─ Kiểm tra CK
    │                             ├─ Xác nhận (PAID)
    │                             ├─ Giao tài khoản (DELIVERED)
    │                             │  ↓ Hệ thống tự động:
    │                             │  - Lấy acc từ kho
    │                             │  - Trừ tồn kho
    │                             │  - Lưu vào order item
    ├─ Xem hóa đơn ←──────────── │
    └─ Nhận tài khoản             │
```

---

## 🎨 Tính năng

### Khách hàng
- ✅ Trang chủ với Hero section ấn tượng
- ✅ Danh sách sản phẩm với 8 danh mục
- ✅ Tìm kiếm & lọc theo danh mục
- ✅ Giỏ hàng (Zustand + localStorage)
- ✅ Thanh toán với QR VietQR động
- ✅ Hóa đơn điện tử với export PDF
- ✅ Tra cứu đơn hàng

### Admin
- ✅ Dashboard với stats real-time
- ✅ Quản lý sản phẩm (CRUD)
- ✅ Quản lý đơn hàng + xác nhận TT + giao hàng
- ✅ Kho tài khoản (import bulk)
- ✅ Quản lý khách hàng
- ✅ Giao tài khoản tự động

### Technical
- ✅ Dark Mode mặc định
- ✅ Responsive Mobile First
- ✅ JWT Authentication
- ✅ Input sanitization (chống XSS)
- ✅ Parameterized queries (chống SQL Injection via Prisma)
- ✅ SEO Meta tags

---

## 🛡️ Bảo mật

- JWT với HTTP-only cookies
- Bcrypt password hashing (12 rounds)
- Input sanitization với regex HTML strip
- Prisma ORM ngăn SQL Injection
- Role-based access control (ADMIN/USER)
- CORS protection via Next.js

---

## 🚢 Deploy lên Production

### Vercel + Neon DB (Recommended)

```bash
# 1. Tạo DB tại neon.tech
# 2. Copy DATABASE_URL vào Vercel env vars
# 3. Deploy
vercel --prod
npm run db:push
npm run db:seed
```

### VPS với PM2

```bash
npm run build
pm2 start npm --name "digishop" -- start
```

---

## 📝 Tuỳ chỉnh

### Đổi thông tin cửa hàng
Chỉnh sửa các biến `NEXT_PUBLIC_*` trong `.env`

### Thêm ngân hàng VietQR
Xem danh sách bank ID tại: https://api.vietqr.io/v2/banks

### Thêm danh mục mới
Cập nhật `ProductCategory` trong `prisma/schema.prisma` và `src/types/index.ts`

---

Made with ❤️ by DigiShop Team
