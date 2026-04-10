# 🛒 Forever — Full-Stack E-Commerce Platform

A full-stack e-commerce web application built with the **MERN stack** (MongoDB, Express.js, React, Node.js). The platform features a **customer-facing storefront**, a **separate admin dashboard**, and a **RESTful backend API** — all working together to deliver a complete online shopping experience.

---

## 📸 Tech Stack

| Layer       | Technology                                                                 |
| ----------- | -------------------------------------------------------------------------- |
| **Frontend**| React 19, React Router v7, Tailwind CSS v4, Axios, React Toastify, Vite 7 |
| **Admin**   | React 19, React Router v7, Tailwind CSS v4, Axios, React Toastify, Vite 7 |
| **Backend** | Node.js, Express 5, Mongoose (MongoDB), JWT, Bcrypt, Cloudinary, Multer   |
| **Database**| MongoDB Atlas                                                              |
| **Storage** | Cloudinary (Product Images)                                                |

---

## ✨ Features

### 🛍️ Customer Storefront (Frontend)
- **Home Page** — Hero banner, Latest Collection, Best Sellers, Store Policies, Newsletter subscription
- **Product Catalog** — Browse all products with multi-level filtering (Category + Subcategory) and sorting (Price: Low-High, High-Low)
- **Product Search** — Real-time search bar with context-aware visibility (appears only on the Collection page)
- **Product Detail Page** — Multi-image gallery with thumbnail navigation, size selector, related products section, and add-to-cart functionality
- **Shopping Cart** — Add/remove items, update quantities, size-specific cart entries, dynamic cart total with shipping fee calculation
- **Checkout Flow** — Delivery information form with payment method selection (Stripe / Razorpay / COD)
- **User Authentication** — Register & Login with JWT-based `httpOnly` cookie authentication
- **Protected Routes** — Orders and checkout pages are guarded; unauthenticated users are redirected to login
- **Session Persistence** — Auto-verifies user session on page load via cookie-based token verification
- **Responsive Design** — Mobile-first layout with a hamburger sidebar menu for small screens
- **Static Pages** — About Us, Contact Us with store info and newsletter

### 🔧 Admin Dashboard
- **Secure Admin Login** — Role-based authentication (Admin role verified via JWT + database lookup)
- **Add Products** — Upload up to 4 images per product, set name, description, price, category, subcategory, sizes, and bestseller flag
- **Product List** — View all products in a responsive table with image preview, category, and price
- **Delete Products** — Remove products with automatic Cloudinary image cleanup (batch deletion using `delete_resources`)
- **Session Persistence** — Admin session verified on page load; conditional rendering between login and dashboard
- **Sidebar Navigation** — Quick access to Add Items, List Items, and Orders sections

### ⚙️ Backend API
- **RESTful Architecture** — 11 API endpoints across 2 resource routers (User & Product)
- **Authentication System** — JWT token generation with configurable expiry, stored in `httpOnly` secure cookies
- **Role-Based Access Control** — Separate middleware for User (`userAuth`) and Admin (`adminAuth`) authorization
- **Password Security** — Bcrypt hashing with salt rounds (10 for registration, 12 for admin seeding)
- **Input Validation** — Email validation via `validator` library, password length enforcement
- **Image Upload Pipeline** — Multer for multipart form handling → Cloudinary for cloud storage → secure URL persistence in MongoDB
- **Cloudinary Cleanup** — Automatic extraction of `public_id` from URLs and batch deletion when products are removed
- **CORS Configuration** — Dynamic origin whitelist supporting both frontend and admin dev servers with credentials enabled
- **Database Seeding** — Standalone seed script to bootstrap the initial admin account from environment variables
- **Error Handling** — Consistent JSON error responses with appropriate HTTP status codes across all endpoints

---

## 🏗️ Project Structure

```
forever/
├── backend/                    # Express.js REST API
│   ├── src/
│   │   ├── config/             # Cloudinary configuration
│   │   ├── controllers/        # Business logic (userController, productController)
│   │   ├── db/                 # MongoDB connection setup
│   │   ├── middleware/         # Auth guards (adminAuth, userAuth) & Multer config
│   │   ├── models/             # Mongoose schemas (User, Product)
│   │   ├── routes/             # Route definitions (userRouter, productRouter)
│   │   ├── app.js              # Express app setup, middleware, routes
│   │   └── server.js           # Server entry point, DB + Cloudinary init
│   ├── seed.js                 # Admin account seeder script
│   └── package.json
│
├── frontend/                   # Customer-facing React SPA
│   ├── src/
│   │   ├── assets/             # Static images and icons
│   │   ├── components/         # 13 reusable UI components
│   │   ├── context/            # ShopContext (global state management)
│   │   ├── pages/              # 9 page components
│   │   ├── App.jsx             # Route definitions + layout
│   │   └── main.jsx            # App entry point with Router + Context Provider
│   └── package.json
│
├── admin/                      # Admin dashboard React SPA
│   ├── src/
│   │   ├── assets/             # Admin-specific assets
│   │   ├── components/         # Navbar, Sidebar
│   │   ├── pages/              # Add, List, Login, Orders
│   │   ├── App.jsx             # Admin routes + auth gate
│   │   └── main.jsx            # Entry point
│   └── package.json
│
└── README.md
```

---

## 🔌 API Endpoints

### User Routes (`/api/user`)

| Method | Endpoint         | Auth       | Description                   |
| ------ | ---------------- | ---------- | ----------------------------- |
| POST   | `/register`      | Public     | Register a new user           |
| POST   | `/login`         | Public     | User login                    |
| GET    | `/verifyUser`    | User Auth  | Verify user session           |
| POST   | `/logout`        | User Auth  | Logout user (clear cookie)    |
| POST   | `/loginAdmin`    | Public     | Admin login                   |
| GET    | `/verifyAdmin`   | Admin Auth | Verify admin session          |
| POST   | `/logoutAdmin`   | Admin Auth | Logout admin (clear cookie)   |

### Product Routes (`/api/product`)

| Method | Endpoint   | Auth       | Description                         |
| ------ | ---------- | ---------- | ----------------------------------- |
| POST   | `/add`     | Admin Auth | Add product with up to 4 images     |
| POST   | `/remove`  | Admin Auth | Delete product + Cloudinary cleanup |
| POST   | `/single`  | Public     | Get single product details          |
| GET    | `/list`    | Public     | Get all products                    |

---

## 🗃️ Database Schemas

### User Schema
```js
{
  name:     String (required),
  email:    String (required, unique),
  password: String (required, hashed),
  cartData: Object (default: {}),
  role:     String (enum: ["User", "Admin"], default: "User"),
  timestamps: true
}
```

### Product Schema
```js
{
  name:        String (required),
  description: String (required),
  price:       Number (required),
  image:       Array  (required, Cloudinary URLs),
  category:    String (required, e.g., "Men", "Women", "Kids"),
  subcategory: String (required, e.g., "Topwear", "Bottomwear", "Winterwear"),
  sizes:       Array  (required, e.g., ["S", "M", "L", "XL"]),
  bestseller:  Boolean,
  date:        Number (required, epoch timestamp),
  timestamps:  true
}
```

---

## 🚀 Getting Started

### Prerequisites
- Node.js (v18+)
- MongoDB Atlas account
- Cloudinary account

### 1. Clone the repository
```bash
git clone https://github.com/Bhushan144/forever.git
cd forever
```

### 2. Backend Setup
```bash
cd backend
npm install
```

Create a `.env` file in the `backend/` directory:
```env
PORT=4000
MONGODB_URI=your_mongodb_connection_string
DB_NAME=your_database_name
JWT_SECRET_KEY=your_jwt_secret
JWT_TOKEN_EXPIRY=7d
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=your_admin_password
NODE_ENV=development
```

Seed the admin account:
```bash
node seed.js
```

Start the backend server:
```bash
npm run server
```

### 3. Frontend Setup
```bash
cd frontend
npm install
```

Create a `.env` file in the `frontend/` directory:
```env
VITE_BACKEND_URL=http://localhost:4000
```

Start the development server:
```bash
npm run dev
```

### 4. Admin Panel Setup
```bash
cd admin
npm install
```

Create a `.env` file in the `admin/` directory:
```env
VITE_BACKEND_URL=http://localhost:4000
```

Start the admin dashboard:
```bash
npm run dev
```

---

## 🔐 Security Highlights

- JWT tokens stored in **httpOnly cookies** (not localStorage) — prevents XSS token theft
- **Bcrypt password hashing** with configurable salt rounds
- **Role-based middleware** — separate auth guards for User and Admin routes
- **CORS whitelist** — only allowed origins can make credentialed requests
- **Secure cookie flag** — automatically enabled in production environment

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

## 👤 Author

**Bhushan Pagar**  
- GitHub: [@Bhushan144](https://github.com/Bhushan144)  
- Email: pagarbhushan02@gmail.com
