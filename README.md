# FarmConnect Backend

FarmConnect Backend is the Node.js, Express, and MariaDB/MySQL API for the FarmConnect agricultural marketplace. Farmers can publish produce listings, buyers can send inquiries and order requests, and admins can manage users, products, orders, and categories.

## Tech Stack

- Node.js and Express.js
- MariaDB/MySQL with `mysql2`
- JWT authentication with `jsonwebtoken`
- Password hashing with `bcrypt`
- Image uploads with `multer`
- Environment variables with `dotenv`

## Folder Structure

- `config/db.js`: Creates the MariaDB/MySQL connection pool.
- `controllers/`: Holds request logic for authentication, products, orders, inquiries, profiles, admin tools, and categories.
- `middleware/`: Handles JWT authentication, role checks, and image uploads.
- `routes/`: Defines API endpoints and connects middleware and controllers.
- `uploads/`: Stores uploaded product images.
- `database/schema.sql`: Creates database tables.
- `database/seed.sql`: Adds sample users, profiles, categories, products, orders, and inquiries.
- `server.js`: Starts Express and registers API routes.

## Prerequisites

- Node.js 18 or newer
- npm
- MariaDB or MySQL

## Setup Instructions

### 1. Install Dependencies

```bash
npm install
```

### 2. Configure Environment Variables

Copy the environment template:

```bash
cp .env.example .env
```

Edit `.env` with your local database credentials:

```env
PORT=5000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_database_password
DB_NAME=farmconnect_learning
JWT_SECRET=replace_with_a_long_random_secret
JWT_EXPIRES_IN=1d
CLIENT_URL=http://localhost:5173
CLIENT_URLS=http://localhost:5173,http://127.0.0.1:5173
```

### 3. Set Up the Database

Create your separate local learning database and import the supplied schema and sample data.

```bash
mysql -u root -p -e "CREATE DATABASE farmconnect_learning;"
```

Import the schema:

```bash
mysql -u root -p farmconnect_learning < database/schema.sql
```

Import sample data:

```bash
mysql -u root -p farmconnect_learning < database/seed.sql
```

Ensure the SQL scripts do not select or recreate a different database.

### 4. Start the Backend

```bash
npm run dev
```

The backend runs at:

`http://localhost:5000`

## Environment Variables

- `PORT`: Express server port.
- `DB_HOST`: Database host.
- `DB_USER`: Database username.
- `DB_PASSWORD`: Database password.
- `DB_NAME`: Database name.
- `JWT_SECRET`: Secret used to sign JWTs.
- `JWT_EXPIRES_IN`: Token lifetime.
- `CLIENT_URL`: Main frontend URL.
- `CLIENT_URLS`: Additional frontend URLs allowed by CORS.

## Run Commands

Development:

```bash
npm run dev
```

Production-style startup:

```bash
npm start
```

## Verify the Backend

Open:

`http://localhost:5000`

Expected response:

```json
{
  "message": "FarmConnect API is running."
}
```

## Default Login Accounts

All seeded accounts use the password `password123`.

- Admin: `admin@farmconnect.test`
- Farmer: `farmer1@farmconnect.test`
- Buyer: `buyer1@farmconnect.test`

## Additional Features

The FarmConnect application was extended with three additional features:

1. **Shopping Cart:** Allows buyers to manage products they intend to purchase.
2. **Seller Analytics:** Allows farmers to view relevant seller statistics.
3. **Notifications:** Provides notification access for both buyers and farmers.

## JWT Authentication

When a user logs in, the API returns a JWT. The frontend stores the token in `localStorage` and sends it in the `Authorization` header as `Bearer <token>`.

The `authMiddleware` verifies the token and attaches the decoded user to `req.user`.

## Role-Based Access Control

The `roleMiddleware` checks `req.user.role`. Product creation is restricted to farmers, while admin routes are restricted to administrators.

Controllers also perform ownership checks to ensure farmers can only manage their own listings.

## Image Uploads

Product image uploads use Multer in `middleware/uploadMiddleware.js`.

Images are saved in `uploads/` and served publicly through `/uploads/`.

The frontend sends product forms using `multipart/form-data`.

## API Documentation

### Auth

- `POST /api/auth/register`: Register a farmer or buyer.
- `POST /api/auth/login`: Login and receive a JWT.
- `GET /api/auth/me`: Get the logged-in user.

### Products

- `GET /api/products`: Browse available products.
- `GET /api/products/:id`: Get one product.
- `POST /api/products`: Farmer creates a product.
- `PUT /api/products/:id`: Farmer edits own product.
- `DELETE /api/products/:id`: Farmer or admin removes a listing.
- `GET /api/products/farmer/my-products`: Farmer views own listings.

### Orders

- `POST /api/orders`: Buyer sends an order request.
- `GET /api/orders/my-orders`: Buyer views own orders.
- `GET /api/orders/farmer`: Farmer views orders for own products.
- `PUT /api/orders/:id/status`: Farmer/admin updates order status.

### Inquiries

- `POST /api/inquiries`: Buyer sends an inquiry.
- `GET /api/inquiries/my-inquiries`: Buyer views own inquiries.
- `GET /api/inquiries/farmer`: Farmer views inquiries for own products.
- `PUT /api/inquiries/:id/status`: Farmer/admin updates inquiry status.

### Profiles

- `GET /api/profile`: Get current user profile.
- `PUT /api/profile/farmer`: Create/update farmer profile.
- `PUT /api/profile/buyer`: Create/update buyer profile.

### Categories

- `GET /api/categories`: List active categories.
- `POST /api/categories`: Admin creates a category.
- `PUT /api/categories/:id`: Admin updates a category.

### Admin

- `GET /api/admin/stats`: Basic system statistics.
- `GET /api/admin/users`: List all users.
- `GET /api/admin/products`: List all products.
- `GET /api/admin/orders`: List all orders.
- `PUT /api/admin/users/:id/status`: Activate or deactivate a user.

## Troubleshooting

- If `npm run dev` fails because `nodemon` is missing, run `npm install`.
- If database login fails, check your database credentials.
- If the database does not exist, create it and import the schema.
- If the frontend receives a CORS error, check `CLIENT_URLS` and restart the backend.

## Security

Never commit your real `.env` file, database passwords, JWT secrets, or private user information to GitHub. Use `.env.example` with placeholder values instead.