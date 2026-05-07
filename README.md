# 🌿 Buliga – Volunteer Management Platform
### IT26 Final Project | PHP + MySQL

---

## 📁 Project Structure

```
buliga/
├── index.php                     ← Public landing page
├── database.sql                  ← Full DB schema + seed data
├── .htaccess                     ← Apache config
│
├── config/
│   └── db.php                    ← PDO database connection
│
├── includes/
│   ├── session.php               ← Auth helpers (requireLogin, requireRole, flash)
│   ├── header.php                ← Shared navbar + flash messages
│   └── footer.php                ← Shared footer + JS includes
│
├── auth/
│   ├── login.php                 ← Login form
│   ├── register.php              ← Registration (student or organizer)
│   └── logout.php                ← Session destroy
│
├── student/
│   ├── dashboard.php             ← Student dashboard (stats + charts + JOINs)
│   ├── events.php                ← Browse & search events (LEFT JOIN)
│   ├── event-detail.php          ← Event detail + register/unregister (CRUD)
│   ├── my-registrations.php      ← All registrations (INNER JOIN, sort)
│   └── profile.php               ← View & edit profile (CRUD Update)
│
├── organizer/
│   ├── dashboard.php             ← Organizer dashboard (3 charts + JOINs)
│   ├── events.php                ← All organizer events (LEFT JOIN, filter)
│   ├── create-event.php          ← Create event (CRUD Create + image upload)
│   ├── edit-event.php            ← Edit/delete event (CRUD Update + Delete)
│   ├── manage-registrations.php  ← Approve/reject/complete volunteers
│   ├── send-announcement.php     ← Send announcements (CRUD Create)
│   └── join-demo.php             ← SQL JOIN demonstration page ⭐
│
├── assets/
│   ├── css/buliga.css            ← Full custom CSS (volunteerism theme)
│   └── js/buliga.js              ← Client JS (search, sort, charts, preview)
│
└── uploads/                      ← Event image uploads
    └── .htaccess                 ← Blocks PHP execution in uploads
```

---

## 🚀 Setup Instructions

### 1. Requirements
- PHP 8.0+
- MySQL 5.7+ / MariaDB 10.4+
- Apache with `mod_rewrite` enabled (XAMPP / WAMP / LAMP)

### 2. Install
```bash
# Clone or copy the buliga/ folder to your web server root
# e.g., C:/xampp/htdocs/buliga/   or   /var/www/html/buliga/
```

### 3. Database Setup
```sql
-- In phpMyAdmin or MySQL CLI:
SOURCE /path/to/buliga/database.sql;
```
Or paste the contents of `database.sql` into phpMyAdmin's SQL tab.

### 4. Configure DB Connection
Edit `config/db.php`:
```php
define('DB_HOST', 'localhost');
define('DB_NAME', 'buliga_db');
define('DB_USER', 'root');       // your MySQL username
define('DB_PASS', '');           // your MySQL password
```

### 5. Run
Visit: `http://localhost/buliga/`

---

## 🔑 Demo Accounts

| Role       | Email                    | Password   |
|------------|--------------------------|------------|
| Organizer  | organizer@buliga.edu     | password   |
| Student    | juan@buliga.edu          | password   |
| Student    | maria@buliga.edu         | password   |
| Student    | carlos@buliga.edu        | password   |

---

## ✅ IT26 Rubric Coverage

| Requirement                        | Where Implemented                                    |
|------------------------------------|------------------------------------------------------|
| Login / Logout / Registration      | `auth/login.php`, `auth/logout.php`, `auth/register.php` |
| Session Management                 | `includes/session.php` (requireLogin, requireRole)   |
| Dashboard with Charts (Chart.js)   | `student/dashboard.php`, `organizer/dashboard.php`   |
| INNER JOIN                         | All dashboards, event-detail, registrations pages    |
| LEFT JOIN                          | Events browser, organizer events, announcements      |
| RIGHT JOIN                         | `organizer/dashboard.php` (volunteer list)           |
| FULL OUTER JOIN                    | `organizer/join-demo.php` (UNION simulation)         |
| SQL JOIN Comments                  | All JOIN queries are annotated with `-- comments`    |
| CRUD – Create                      | Register for event, create event, send announcement  |
| CRUD – Read                        | All listing/detail pages                             |
| CRUD – Update                      | Edit event, approve/reject/complete registrations    |
| CRUD – Delete                      | Delete event (with cascade), cancel registration     |
| Search Functionality               | Events browser (student + organizer) with PHP + JS   |
| Sort Functionality                 | All tables via `data-sortable` JS (client-side)      |
| Image Upload                       | Create/edit event with file validation               |
| Role-Based Access Control          | `requireRole('student')` / `requireRole('organizer')`|
| GitHub Deployment Ready            | Standard file structure, no hardcoded absolute paths |

---

## 🎨 Design Theme

**"Community in Action"** — Earthy greens + warm amber palette, Sora + DM Sans typography,
organic card shapes, volunteerism-inspired UI that feels welcoming and purpose-driven.

---

## 📊 SQL JOIN Summary (organizer/join-demo.php)

```sql
-- INNER JOIN: Only matched rows (registrations with existing student + event)
SELECT r.id, u.full_name, e.title FROM registrations r
INNER JOIN users u  ON r.student_id = u.id
INNER JOIN events e ON r.event_id   = e.id;

-- LEFT JOIN: All events, even those with 0 registrations
SELECT e.title, COUNT(r.id) FROM events e
LEFT JOIN registrations r ON r.event_id = e.id GROUP BY e.id;

-- RIGHT JOIN: All students, even those with 0 registrations
SELECT u.full_name, COUNT(r.id) FROM registrations r
RIGHT JOIN users u ON r.student_id = u.id WHERE u.role='student' GROUP BY u.id;

-- FULL OUTER JOIN (MySQL UNION simulation)
SELECT e.title, u.full_name, r.status FROM events e
LEFT JOIN registrations r ON r.event_id=e.id LEFT JOIN users u ON r.student_id=u.id
UNION
SELECT e2.title, u2.full_name, r2.status FROM users u2
LEFT JOIN registrations r2 ON r2.student_id=u2.id LEFT JOIN events e2 ON r2.event_id=e2.id
WHERE u2.role='student';
```
