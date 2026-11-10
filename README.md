# Laravel 13 Secure API: Token Authentication and RBAC

Integrative Programming & Technologies II, Module 5.

- **Lesson 1:** Sanctum token authentication. Evidence is in `evidence/`.
- **Lesson 2:** Role-based access control with two custom middleware. Evidence is in `screenshots/`.

## RBAC routes

| Endpoint | Method | Middleware | Admin | Student | No token |
|---|---|---|---|---|---|
| `/api/profile` | GET | `auth:sanctum` | 200 | 200 | 401 |
| `/api/students` | GET | `auth:sanctum`, `role:admin,student` | 200 | 200 | 401 |
| `/api/admin/dashboard` | GET | `auth:sanctum`, `admin` | 200 | 403 | 401 |
| `/api/student/dashboard` | GET | `auth:sanctum`, `role:student` | 403 | 200 | 401 |

## How it works

- The `users` table has a `role` column that defaults to `student`.
- Public registration never accepts a `role`. The admin role is assigned only through trusted server-side code (tinker).
- `CheckRole` accepts one or more roles. It returns 401 if there is no user and 403 if the role is not allowed.
- `AdminMiddleware` allows only `role === 'admin'`, with the same 401 and 403 behavior.
- The `role` and `admin` aliases are registered in `bootstrap/app.php`.
- `auth:sanctum` runs first, so the token is checked before the role.

## Local testing steps

1. Start MySQL in XAMPP and create the database `demo_app_integ2`.
2. Run `composer install`, copy `.env.example` to `.env`, set the `DB_*` values, and run `php artisan key:generate`.
3. Run `php artisan migrate`.
4. Create one admin and one student in `php artisan tinker`. Set `role` directly, and choose your own passwords:

```php
$u = User::firstOrNew(['email' => 'admin@example.com']);
$u->name = 'Admin User'; $u->password = Hash::make('CHOOSE_A_PASSWORD'); $u->role = 'admin'; $u->save();
```

5. Run `php artisan serve`.
6. In Postman, send `Accept: application/json`, log in with `POST /api/login`, then use the returned token as a Bearer token.

## Screenshots

`screenshots/` holds the eight Postman tests: admin login, admin allowed, admin denied, student login, student allowed, student denied, shared route, and no token. Tokens are hidden.

## Reflection

**What is the difference between authentication and authorization?**
Authentication checks who you are. In this project Sanctum does it by validating the Bearer token. Authorization checks what you are allowed to do. In this project the role middleware does it by comparing the user's role with the roles the route allows.

**Why does a student get 403 on the admin dashboard, but a request without a token gets 401?**
With no token, Sanctum cannot identify the user, so the request is stopped with 401. A student's token is valid, so the user is identified, but `AdminMiddleware` sees the role is not `admin` and refuses with 403. A 401 means I don't know who you are. A 403 means I know who you are, but you are not allowed here.