# Application Authentication & Authorization Flow


## 1. Accessing the Application
**URL:** `http://localhost:8080/YourApp/`

When a user first visits the application root URL, the request passes through the **AuthFilter**. Since the user has not logged in yet and does not have a valid session, the filter blocks access to protected resources and redirects the user to the **login page**.

## 2. Logging In as Admin
**Credentials:** `admin / password123`

The login form submits a POST request to the `LoginController`. The controller calls `UserDAO.authenticate()`, which:
- Loads the user record from the database
- Verifies the password using **BCrypt**
- Creates a new HTTP session if authentication is successful
- Stores user information (User object, full name, role) in the session

After successful login, the user is redirected to the **dashboard**.

## 3. Viewing the Student List (Admin)
When the admin clicks **“View All Students”**:
- The request goes through AuthFilter and is allowed because a valid session exists.
- The controller loads all students from `StudentDAO`.
- The JSP displays the student list.
- Because the logged‑in user is an **admin**, additional UI options (Edit/Delete) are shown.

Admins have full CRUD permissions.

## 4. Logging Out
When the user logs out:
- The `LogoutController` invalidates the session, removing all stored attributes.
- The user is redirected back to the login page with a success message.

After logout, any attempt to access protected pages results in AuthFilter redirecting back to `/login`.

## 5. Logging In as Regular User
**Credentials:** `john / password123`

The login process is the same, but the session stores a user whose role is `user` instead of `admin`.

When viewing the student list:
- Regular users can **see** the list of students.
- They **cannot** see Edit/Delete buttons.
- The JSP hides admin‑only actions based on the session role.

## 6. Attempting Admin‑Only Actions as Regular User
If a regular user tries to access an admin‑only action directly:
```
/student?action=new
```
The **AdminFilter** runs before the controller and checks the user’s role.
- If the user is not an admin, the filter blocks the request.
- The user is redirected or forwarded to an error message.

This prevents users from bypassing the UI and accessing admin endpoints manually.

---

## Summary
This flow demonstrates a complete authentication and authorization pipeline:
- **AuthFilter**: Ensures only logged‑in users access protected pages
- **Login/Logout Controllers**: Manage session lifecycle
- **Role‑based UI**: Hides admin actions for regular users
- **AdminFilter**: Protects admin‑level URLs from non‑admin accounts

This architecture enforces both **security** and **proper access control** across the entire application.

