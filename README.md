# 💬 Project Chat — Real-Time Chat Application

A full-featured **real-time chat application** built with **Django** and **Django Channels**, supporting one-on-one messaging, group chats, friend management, and a REST API — all powered by WebSockets for instant communication.

---

## ✨ Features

### 🔐 Authentication
- **User Registration** — Create an account with username, email & password
- **Login / Logout** — Session-based authentication with Django's built-in auth
- **Forgot Password** — OTP-based password recovery via email
- **OTP Verification** — Secure one-time password validation with session expiry
- **Password Reset** — Change password after OTP verification

### 💬 Real-Time Messaging
- **One-on-One Chat** — Private messaging between two users via WebSockets
- **Group Chat** — Multi-user chat rooms with real-time message broadcasting
- **Message Persistence** — All messages are stored in the database with timestamps
- **Binary Data Support** — Send and receive binary files (images, documents) over WebSocket

### 👥 Friend System
- **User Discovery** — Browse and search for users on the platform
- **Friend Requests** — Send, accept, or reject friend requests
- **Friends List** — View all your friends and start chatting instantly
- **Duplicate Prevention** — Built-in guards against duplicate friend requests

### 🏠 Group Management
- **Create Groups** — Create chat groups with a name and description
- **Add Members** — Add friends to your group
- **Remove Members** — Remove members from the group
- **Group Details** — View group info and member list
- **Delete Group** — Admin-only group deletion
- **Admin Privileges** — Group creator gets admin rights

### 🌐 REST API
- Full **CRUD API** built with Django REST Framework
- Supports `GET`, `POST`, `PATCH`, and `DELETE` operations
- JSON-based request/response handling with serializers

---

## 🛠️ Tech Stack

| Layer         | Technology                                            |
|---------------|-------------------------------------------------------|
| **Backend**   | Django 5.2, Django REST Framework                     |
| **WebSocket** | Django Channels, Daphne (ASGI Server)                 |
| **Database**  | SQLite (default), MySQL (configurable)                |
| **Frontend**  | Django Templates, HTML, CSS, JavaScript               |
| **Auth**      | Django Built-in Auth, Session-based, OTP via Email    |
| **Async**     | Twisted, Channels Layers (In-Memory)                  |

---

## 📁 Project Structure

```
project_chat/
├── project_chat/          # Django project settings & configuration
│   ├── settings.py        # Project settings (DB, channels, email, etc.)
│   ├── urls.py            # Root URL configuration
│   ├── asgi.py            # ASGI config with WebSocket routing
│   └── wsgi.py            # WSGI config (fallback)
│
├── app/                   # Core chat application
│   ├── models.py          # Group, chat_message, Friend, friend_request models
│   ├── views.py           # Chat views, friend management, home page
│   ├── consumers.py       # WebSocket consumer (AsyncWebsocketConsumer)
│   ├── routing.py         # WebSocket URL routing
│   ├── urls.py            # HTTP URL patterns
│   ├── templates/         # HTML templates (home, chat, friend request, etc.)
│   └── static/            # CSS stylesheets and images
│
├── authentication/        # User authentication app
│   ├── views.py           # Login, register, forgot password, OTP, logout
│   ├── urls.py            # Auth URL patterns
│   └── templates/         # Login, register, OTP, password reset templates
│
├── group_chat/            # Group chat management app
│   ├── models.py          # group_chat model (group, admin, member)
│   ├── views.py           # Create/delete group, add/remove members
│   ├── urls.py            # Group management URL patterns
│   └── templates/         # Group creation, member management templates
│
├── API/                   # REST API app
│   ├── models.py          # User_api model
│   ├── views.py           # Class-based API view (GET, POST, PATCH, DELETE)
│   ├── serializers.py     # DRF serializer for User_api
│   └── urls.py            # API URL patterns
│
├── api_app.py             # Standalone API test script
├── manage.py              # Django management script
├── requirement.txt        # Python dependencies
├── .env                   # Environment variables (SECRET_KEY)
└── .gitignore             # Git ignore rules
```

---

## 🚀 Getting Started

### Prerequisites

- **Python 3.10+**
- **pip** (Python package manager)
- **Git**

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/Adik-09/Project_Chat.git
   cd Project_Chat
   ```

2. **Create a virtual environment**
   ```bash
   python -m venv venv

   # Windows
   venv\Scripts\activate

   # macOS / Linux
   source venv/bin/activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirement.txt
   ```

4. **Set up environment variables**

   Create a `.env` file in the project root:
   ```env
   SECRET_KEY=your-django-secret-key-here
   ```

5. **Run migrations**
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

6. **Create a superuser** (optional, for admin panel)
   ```bash
   python manage.py createsuperuser
   ```

7. **Start the development server**
   ```bash
   python manage.py runserver
   ```

   The app will be available at `http://127.0.0.1:8000/`

---

## 📡 WebSocket Architecture

The app uses **Django Channels** with an **AsyncWebsocketConsumer** for real-time communication:

```
Client (Browser)
    │
    ├── HTTP ──► Django Views (Templates, Auth, API)
    │
    └── WebSocket ──► ws://<host>/ws/<room_name>/
                          │
                          ├── ChatConsumer.connect()    → Authenticate & join group
                          ├── ChatConsumer.receive()    → Broadcast message to group
                          ├── ChatConsumer.chat_message() → Send text to client
                          ├── ChatConsumer.binary_message() → Send binary to client
                          └── ChatConsumer.disconnect() → Leave group
```

- **Protocol Routing**: ASGI application routes HTTP and WebSocket traffic separately
- **Authentication**: WebSocket connections are authenticated via `AuthMiddlewareStack`
- **Channel Layers**: In-memory channel layer for group message broadcasting

---

## 🔗 API Endpoints

### Authentication
| Method | Endpoint                | Description          |
|--------|-------------------------|----------------------|
| GET    | `/ak/login/`            | Login page           |
| POST   | `/ak/login/`            | Authenticate user    |
| GET    | `/ak/register/`         | Registration page    |
| POST   | `/ak/register/`         | Create new account   |
| GET    | `/ak/forget_pass/`      | Forgot password page |
| POST   | `/ak/forget_pass/`      | Send OTP to email    |
| POST   | `/ak/verify_otp/`       | Verify OTP           |
| POST   | `/ak/change_password/`  | Reset password       |
| GET    | `/ak/logout/`           | Logout user          |

### Chat & Friends
| Method | Endpoint                                     | Description              |
|--------|----------------------------------------------|--------------------------|
| GET    | `/ak/home/`                                  | Home page                |
| GET    | `/ak/home/chats/`                            | Friends & groups list    |
| GET    | `/ak/home/add_friends/`                      | Discover users           |
| GET    | `/ak/home/add_friend/<user1>/<user2>/`       | Send friend request      |
| GET    | `/ak/home/friend_request/`                   | View friend requests     |
| POST   | `/ak/home/accept_reject/<sender>/<status>/`  | Accept/reject request    |
| GET    | `/ak/home/chat/<user1>/<user2>/`             | One-on-one chat          |
| GET    | `/ak/home/chat/<group_name>/`                | Group chat               |

### Group Management
| Method | Endpoint                                                    | Description        |
|--------|-------------------------------------------------------------|--------------------|
| GET    | `/ak/home/create_group/`                                    | Create group page  |
| POST   | `/ak/home/create_group/`                                    | Create a group     |
| GET    | `/ak/home/create_group/<grp_name>/`                         | View group members |
| GET    | `/ak/home/create_group/add_mem/<grp_name>/<member>/`        | Add member         |
| GET    | `/ak/home/create_group/delete_mem/<grp_name>/<member>/`     | Remove member      |
| GET    | `/ak/home/group_details/<grp_name>/`                        | Group details      |
| GET    | `/ak/home/delete_group/<grp_name>/`                         | Delete group       |

### REST API
| Method | Endpoint  | Description     |
|--------|-----------|-----------------|
| GET    | `/api/`   | Get all users   |
| POST   | `/api/`   | Create a user   |
| PATCH  | `/api/`   | Update a user   |
| DELETE | `/api/`   | Delete a user   |

---

## 📦 Dependencies

```
Django==5.2.6
channels==4.3.1
daphne==4.2.1
Twisted==25.5.0
djangorestframework
django-cors-headers
django-environ
mysqlclient==2.2.7
```

> See [`requirement.txt`](requirement.txt) for the full list of dependencies.

---

## ⚙️ Configuration

### Database
By default, the project uses **SQLite**. To switch to **MySQL**, update `DATABASES` in `settings.py`:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'your_db_name',
        'USER': 'your_user',
        'PASSWORD': 'your_password',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

### Email (OTP)
The email backend is configured for **console output** by default. To enable real email delivery, update `settings.py`:

```python
EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"
EMAIL_HOST = "smtp.gmail.com"
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_HOST_USER = "your-email@gmail.com"
EMAIL_HOST_PASSWORD = "your-app-password"
```

### Channel Layers
For production, replace the in-memory channel layer with **Redis**:

```python
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [("127.0.0.1", 6379)],
        },
    },
}
```

---

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

<p align="center">
  Built with ❤️ by Aditya Kasture.
</p>
