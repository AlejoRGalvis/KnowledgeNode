# Knowledge Node
### Internal Process Knowledge Base — Scotiabank

Developed by: **Alejandro R. Galvis**
Version: 1.0.0

---

## Overview

Knowledge Node is an internal web application designed to centralize and organize operational processes, procedures, and reference materials for Scotiabank staff. It provides a searchable, role-based knowledge base accessible from any device on the internal network.

---

## Features

- **Role-based access control** — Admin, Supervisor, Consultant, Guest
- **Category system** — RC, D2D, Loans, Disputes, Tsys, Management, Consultants
- **Restricted categories** — Tsys, Management and Consultants visible only to authorized roles
- **Full WYSIWYG editor** — with tables, code blocks, images, and formatting
- **Live search** — instant results as you type
- **Export to PDF** — any article can be exported
- **Share articles** — direct link sharing
- **Dark mode** — toggle between light and dark themes
- **Image uploads** — stored locally on the server
- **Dashboard** — statistics for admins (most viewed, recent articles)
- **LAN accessible** — any device on the same network can access it

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | HTML, CSS, JavaScript (Vanilla) |
| Backend | Node.js v24+ with Express |
| Database | SQLite (via sqlite3) |
| Authentication | JWT (JSON Web Tokens) |
| Password Security | bcrypt |
| File Uploads | multer |

---

## System Requirements

- **Node.js** v18 or higher
- **npm** v8 or higher
- **Operating System:** Windows, Linux, or macOS
- **RAM:** Minimum 512MB
- **Disk:** Minimum 200MB free space

---

## Installation

### 1. Clone or copy the project folder

```
Knowledge-node/
├── src/
│   ├── config/
│   ├── controllers/
│   ├── middleware/
│   ├── models/
│   └── routes/
├── public/
│   ├── knowledge-node.html
│   └── uploads/
├── package.json
└── .env
```

### 2. Install dependencies

Navigate to the project folder and run:

```bash
npm install
```

This will install all required packages listed in `package.json`. No internet connection required after this step if `node_modules` is included.

### 3. Configure environment variables

Create a `.env` file in the root of the project with the following values:

```
PORT=3000
JWT_SECRET=YourSecureSecretKeyHere
```

> **Important:** Change `JWT_SECRET` to a strong, unique value before deploying to production.

### 4. Start the server

```bash
node src/server.js
```

The server will start and display:

```
Server running on port 3000
Connected to SQLite database.
Tables ready.
```

### 5. Access the application

Open a browser and navigate to:

```
http://localhost:3000/knowledge-node.html
```

Or from any device on the same network:

```
http://<server-ip>:3000/knowledge-node.html
```

---

## Default Users

| Username | Role | Access Level |
|----------|------|-------------|
| admin | Admin | Full access — create, edit, delete articles |
| consultant | Consultant | Read + restricted categories |
| supervisor | Supervisor | Read + restricted categories |

> **Note:** Passwords are set during initial setup and stored as bcrypt hashes. They are never stored in plain text.

To create or change users, use the following command:

```bash
# Create new user
node -e "
const bcrypt = require('bcrypt');
const db = require('./src/config/database');
bcrypt.hash('NewPassword123!', 10, (err, hash) => {
  db.run('INSERT INTO users (username, password_hash, role) VALUES (?, ?, ?)',
    ['newuser', hash, 'consultant'],
    () => { console.log('User created'); process.exit(); }
  );
});
"
```

---

## Database

The application uses **SQLite** — a lightweight, file-based database. The database file is located at:

```
Knowledge-node/database.db
```

### Tables

**users**
| Column | Type | Description |
|--------|------|-------------|
| id | INTEGER | Auto-increment primary key |
| username | TEXT | Unique username |
| password_hash | TEXT | bcrypt hashed password |
| role | TEXT | admin / supervisor / consultant / viewer |
| created_at | DATETIME | Creation timestamp |

**articles**
| Column | Type | Description |
|--------|------|-------------|
| id | TEXT | UUID primary key |
| title | TEXT | Article title |
| content | TEXT | HTML content |
| category | TEXT | RC / D2D / Loans / Disputes / Tsys / Management / Consultants |
| tags | TEXT | JSON array of tags |
| created_by | TEXT | Username of creator |
| created_at | DATETIME | Creation timestamp |
| updated_at | DATETIME | Last update timestamp |

---

## File Uploads

Uploaded images are stored in:

```
Knowledge-node/public/uploads/
```

Maximum file size: **5MB per image**
Accepted formats: JPEG, PNG, GIF, WebP

---

## API Endpoints

| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| POST | /api/auth/login | Public | User login |
| POST | /api/auth/register | Public | Create user |
| POST | /api/auth/change-password | Public | Change password |
| GET | /api/articles | Public | Get all articles |
| POST | /api/articles | Admin | Create article |
| PUT | /api/articles/:id | Admin | Update article |
| DELETE | /api/articles/:id | Admin | Delete article |
| POST | /api/upload | Admin | Upload image |

---

## Security Considerations

- All passwords are hashed using **bcrypt** (cost factor 10)
- Authentication uses **JWT tokens** with 8-hour expiration
- Restricted categories are enforced on the **frontend** by role
- File uploads are validated by type and size
- No external network calls — fully self-contained

> **Note for IT:** For production deployment, it is recommended to:
> 1. Place the application behind a reverse proxy (nginx or IIS)
> 2. Enable HTTPS with an internal SSL certificate
> 3. Migrate the database from SQLite to SQL Server or PostgreSQL
> 4. Set up automated database backups

---

## Running as a Windows Service (Optional)

To keep the server running automatically, install **pm2**:

```bash
npm install -g pm2
pm2 start src/server.js --name "knowledge-node"
pm2 startup
pm2 save
```

---

## Support

For technical questions or issues, contact:
**Alejandro R. Galvis** — Internal IT / Operations Team

---

*Knowledge Node v1.0.0 — Scotiabank Internal Tool*
