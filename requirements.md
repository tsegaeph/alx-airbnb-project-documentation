## 1. User Authentication & Authorization

### Functional Requirements
- Users can **register**, **login**, and **logout**.
- Users can update their **profile information**.
- Authentication must be **secure** using JWT.
- Support for **role-based access control (RBAC)**:
  - Guest
  - Host
  - Admin

### API Endpoints
| Method | Endpoint                | Description                   | Auth Required |
|--------|-------------------------|-------------------------------|---------------|
| POST   | `/api/v1/auth/register` | Register new user             | No            |
| POST   | `/api/v1/auth/login`    | Login and retrieve JWT        | No            |
| POST   | `/api/v1/auth/logout`   | Logout and invalidate JWT     | Yes           |
| GET    | `/api/v1/users/me`      | Get current user profile      | Yes           |
| PUT    | `/api/v1/users/me`      | Update user profile           | Yes           |

### Input / Output Examples

**Register Request**
```json
{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "securePass123"
}


Register Response

{
  "id": "u12345",
  "username": "john_doe",
  "email": "john@example.com",
  "role": "guest",
  "token": "jwt_token_here"
}


##Login Request

{
  "email": "john@example.com",
  "password": "securePass123"
}


Login Response
{
  "token": "jwt_token_here",
  "userId": "u12345",
  "role": "guest"
}

### Validation Rules
- Email must be **unique** and in a valid format.
- Password must be at least **8 characters**, include a **number** and a **special character** (recommended).
- Username must be **unique**.
- Role must be one of: `guest`, `host`, `admin`.

### Security & Performance
- Passwords hashed with **bcrypt** (or equivalent, work factor configurable).
- JWT signed with strong secret or asymmetric keys; expiration: **24h**; support refresh tokens.
- Authentication endpoints should respond within **200ms** under typical load.
