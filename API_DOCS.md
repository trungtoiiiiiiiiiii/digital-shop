# API Documentation — DigiShop

Base URL: `http://localhost:3000/api`

## Authentication

Tất cả Admin API yêu cầu header:
```
Authorization: Bearer <JWT_TOKEN>
```

Token lấy từ `POST /api/auth/login`.

---

## 🔐 Auth

### POST /api/auth/login
Đăng nhập admin.

**Request Body:**
```json
{
  "email": "admin@digitalshop.vn",
  "password": "Admin@123456"
}
```

**Response 200:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "cuid",
    "name": "Admin",
    "email": "admin@digitalshop.vn",
    "role": "ADMIN"
  }
}
```

---

## 📦 Products

### GET /api/products
Lấy danh sách sản phẩm.

**Query params:**
- `category` — lọc theo danh mục (NETFLIX | SPOTIFY | YOUTUBE | CHATGPT | CANVA | CAPCUT | GAMING | OTHER)
- `search` — tìm kiếm theo tên/mô tả

**Response 200:**
```json
[
  {
    "id": "cuid",
    "name": "Netflix Premium 4K",
    "description": "...",
    "price": 65000,
    "category": "NETFLIX",
    "imageUrl": null,
    "inStock": true,
    "stockCount": 15,
    "featured": true,
    "createdAt": "2025-01-01T00:00:00.000Z",
    "updatedAt": "2025-01-01T00:00:00.000Z"
  }
]
```

### POST /api/products [Admin]
Thêm sản phẩm mới.

**Request Body:**
```json
{
  "name": "Netflix Premium 4K",
  "description": "Mô tả sản phẩm",
  "price": 65000,
  "category": "NETFLIX",
  "stockCount": 10,
  "featured": true,
  "imageUrl": "https://example.com/image.jpg"
}
```

### PUT /api/products/:id [Admin]
Cập nhật sản phẩm.

### DELETE /api/products/:id [Admin]
Xoá sản phẩm.

---

## 🛒 Orders

### POST /api/orders
Tạo đơn hàng mới (public — không cần auth).

**Request Body:**
```json
{
  "customerName": "Nguyễn Văn A",
  "customerPhone": "0901234567",
  "note": "Ghi chú",
  "totalAmount": 65000,
  "items": [
    {
      "productId": "cuid",
      "quantity": 1,
      "price": 65000
    }
  ]
}
```

**Response 201:**
```json
{
  "id": "cuid",
  "transactionCode": "DSABC123DEF",
  "customerName": "Nguyễn Văn A",
  "customerPhone": "0901234567",
  "totalAmount": 65000,
  "status": "PENDING",
  "createdAt": "2025-01-01T00:00:00.000Z",
  "items": [...]
}
```

### GET /api/orders [Admin]
Danh sách đơn hàng (phân trang).

**Query params:**
- `page` — trang (default: 1)
- `limit` — số bản ghi (default: 20)
- `status` — lọc theo trạng thái
- `search` — tìm kiếm

**Response 200:**
```json
{
  "orders": [...],
  "total": 100,
  "page": 1,
  "limit": 20
}
```

### GET /api/orders/:id
Lấy chi tiết đơn hàng (public — cần biết ID).

### PATCH /api/orders/:id [Admin]
Cập nhật trạng thái đơn hàng.

**Request Body:**
```json
{ "action": "mark_paid" }
// hoặc
{ "action": "deliver" }
// hoặc
{ "action": "CANCELLED" }
```

**Actions:**
- `mark_paid` — xác nhận đã thanh toán → status = PAID
- `deliver` — giao tài khoản tự động → status = DELIVERED (cần có tài khoản trong kho)
- `CANCELLED` — huỷ đơn hàng

### GET /api/orders/lookup?q={query}
Tra cứu đơn hàng theo mã giao dịch hoặc SĐT (public).

---

## 📊 Admin Stats

### GET /api/admin/stats [Admin]
Dashboard statistics.

**Response 200:**
```json
{
  "todayRevenue": 650000,
  "todayOrders": 10,
  "monthRevenue": 15000000,
  "monthOrders": 230,
  "totalOrders": 1500,
  "pendingOrders": 5,
  "topProducts": [
    { "name": "Netflix Premium 4K", "count": 45, "revenue": 2925000 }
  ],
  "recentOrders": [...]
}
```

---

## 🗄️ Account Stock

### GET /api/admin/accounts [Admin]
Danh sách tài khoản trong kho.

**Query params:**
- `productId` — lọc theo sản phẩm
- `available=true` — chỉ tài khoản chưa dùng

### POST /api/admin/accounts [Admin]
Thêm tài khoản vào kho.

**Request Body:**
```json
{
  "productId": "cuid",
  "accounts": [
    {
      "email": "acc1@gmail.com",
      "password": "password123",
      "note": "Ghi chú"
    },
    {
      "email": "acc2@gmail.com",
      "password": "password456"
    }
  ]
}
```

**Response 201:**
```json
{ "added": 2 }
```

---

## ⚠️ Error Responses

```json
{
  "message": "Mô tả lỗi"
}
```

| Status | Ý nghĩa |
|--------|---------|
| 400 | Bad Request — dữ liệu không hợp lệ |
| 401 | Unauthorized — chưa đăng nhập hoặc không có quyền |
| 404 | Not Found — không tìm thấy resource |
| 500 | Internal Server Error — lỗi server |

---

## 📋 Database Schema

### Enums
- `Role`: ADMIN, USER
- `OrderStatus`: PENDING, PAID, DELIVERED, CANCELLED
- `ProductCategory`: NETFLIX, SPOTIFY, YOUTUBE, CHATGPT, CANVA, CAPCUT, GAMING, OTHER

### Models
- **User** — tài khoản người dùng/admin
- **Product** — sản phẩm
- **AccountStock** — kho tài khoản số
- **Order** — đơn hàng
- **OrderItem** — chi tiết đơn hàng
- **SiteSettings** — cài đặt hệ thống
