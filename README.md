# -CPS---Classroom-Presence-System
## Author
- Твое Име Презиме — Давид Стојчевски 102723 и Мартин Ѓоргиев 102722
- Факултет за информатика - Штип
- Предмет: Развој на мобилни апликации
- Академска година: 2025/2026

---

## About The Project

CPS (Classroom Presence System) is a mobile-based attendance tracking system 
designed for university classrooms. The system eliminates paper-based attendance 
sheets by allowing students to register their presence digitally using NFC technology.

When a student enters the classroom, they open the CPS Student app and tap their 
phone to the teacher's phone. The teacher's app reads the student's identity via 
NFC and registers the attendance instantly. All records are stored locally on the 
teacher's device and can be synced to the server at any time. A web dashboard 
provides real-time statistics and attendance history.

---

## How It Works
┌─────────────────┐         NFC Tap          ┌─────────────────┐
│  Student Phone  │ ───────────────────────► │  Teacher Phone  │
│  (HCE Card)     │                          │  (NFC Reader)   │
└─────────────────┘                          └────────┬────────┘
│
│ HTTP Sync
▼
┌─────────────────┐
│  PHP Backend    │
│  MySQL Database │
└────────┬────────┘
│
│
▼
┌─────────────────┐
│  Web Dashboard  │
│  Charts + Table │
└─────────────────┘

1. Student opens CPS Student app and logs in
2. HCE service activates — phone acts as an NFC card
3. Student taps phone to teacher's phone
4. Teacher app reads student ID and name via NFC
5. Attendance record saved locally in Room database
6. Teacher clicks Sync — all records sent to PHP backend
7. Web dashboard displays live attendance data and charts

---

## Project Structure
cps-project/
│
├── backend/                    # PHP Backend + Web Dashboard
│   ├── api/
│   │   ├── login.php           # Authentication endpoint
│   │   ├── attendance_post.php # Post attendance records
│   │   ├── attendance_get.php  # Get attendance records
│   │   └── statistics.php      # Statistics for dashboard
│   ├── config/
│   │   └── database.php        # Database connection + JWT config
│   ├── middleware/
│   │   └── auth.php            # JWT token validation
│   ├── dashboard/
│   │   ├── index.html          # Web dashboard UI
│   │   ├── style.css           # Dashboard styles
│   │   └── app.js              # Dashboard logic + Chart.js
│   └── cps_db.sql              # Database export (import this)
│
├── teacher-app/                # Android App for Teachers
│   └── app/src/main/
│       ├── java/com/cps/teacher/
│       │   ├── LoginActivity.java      # Login screen
│       │   ├── MainActivity.java       # NFC reader + attendance list
│       │   ├── AttendanceAdapter.java  # RecyclerView adapter
│       │   ├── Constants.java          # API URL configuration
│       │   └── db/
│       │       ├── AppDatabase.java        # Room database
│       │       ├── AttendanceDao.java      # Database queries
│       │       └── AttendanceRecord.java   # Data model
│       └── res/
│           ├── layout/         # XML layouts
│           └── xml/            # NFC tech filter config
│
├── student-app/                # Android App for Students
│   └── app/src/main/
│       ├── java/com/cps/student/
│       │   ├── LoginActivity.java      # Login screen
│       │   ├── MainActivity.java       # NFC status screen
│       │   ├── Constants.java          # API URL configuration
│       │   └── StudentCardService.java # HCE NFC card emulation
│       └── res/
│           ├── layout/         # XML layouts
│           └── xml/            # HCE APDU service config
│
└── README.md

---

## Technology Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Backend | PHP 8 | REST API server |
| Database | MySQL | Data storage |
| Server | Apache (XAMPP) | Local web server |
| Mobile | Android + Java | Teacher and Student apps |
| NFC | Host Card Emulation (HCE) | Student phone as NFC card |
| Local DB | Room Database | Offline attendance storage |
| Networking | OkHttp3 | HTTP requests |
| Auth | JWT (custom) + bcrypt | Secure authentication |
| Dashboard | HTML + CSS + JavaScript | Web interface |
| Charts | Chart.js | Attendance visualization |

---

## Features

### Teacher App
- Secure login (teachers only)
- NFC reader using IsoDep protocol
- Real-time attendance registration via NFC tap
- Local storage with Room database (works offline)
- Attendance list with student name, ID, and timestamp
- One-tap sync to backend server
- Pending records counter
- Duplicate prevention (one record per student per day)

### Student App
- Secure login (students only)
- NFC card emulation via Host Card Emulation (HCE)
- Automatic activation when app is open
- Works on all Android 5+ devices with NFC

### Web Dashboard
- Secure login
- Today's attendance counter
- Total records counter
- Bar chart — attendance per course
- Line chart — attendance last 7 days
- Full attendance table with filters
- Filter by date

### Backend API
- JWT authentication
- Role-based access (student / teacher / admin)
- Attendance sync with duplicate prevention
- Statistics endpoint
- CORS enabled for web dashboard

---

## Setup Instructions

### Requirements
- XAMPP (Apache + MySQL)
- Android Studio (for building apps)
- Two Android devices with NFC (for real testing)
- Both devices on the same WiFi network as the server

---

### Step 1 — Backend Setup

1. Install XAMPP from https://www.apachefriends.org
2. Copy the `backend/` folder to:
C:/xampp/htdocs/cps
3. Open XAMPP Control Panel and start **Apache** and **MySQL**
4. Open your browser and go to:
http://localhost/phpmyadmin
5. Click **New** → name it `cps_db` → click **Create**
6. Click on `cps_db` → click **Import**
7. Choose the file `backend/cps_db.sql` → click **Go**
8. Open the web dashboard at:
http://localhost/cps/dashboard/index.html

---

### Step 2 — Android Apps Setup

1. Open Android Studio
2. Open `teacher-app/` as a project
3. Open `student-app/` as a separate project
4. In both projects, open `Constants.java` and configure:

```java
// For emulator testing:
private static final boolean USE_EMULATOR = true;

// For real device testing:
private static final boolean USE_EMULATOR = false;
// Also update the IP address to your computer's local IP:
// Example: "http://192.168.1.7/cps/api"
```

5. Find your computer's IP address:
   - Open Command Prompt → type `ipconfig`
   - Look for **IPv4 Address** (example: 192.168.1.7)
   - Update the IP in `Constants.java` in both apps

6. Build and install:
   - Connect Android device via USB
   - Enable **USB Debugging** in Developer Options
   - Click **Run** in Android Studio

---

### Step 3 — NFC Testing on Real Devices

1. Enable NFC on both phones:
   - Settings → Connections → NFC → ON
2. Install Teacher App on one phone
3. Install Student App on the other phone
4. Log in on both devices
5. Touch the **back of the two phones together**
6. Teacher app will show: ✓ Student registered!
7. Click **Sync Now** to send records to the server

---

## Test Accounts

| Role | Email | Password |
|------|-------|----------|
| Teacher | teacher@cps.com | password |
| Student | student@cps.com | password |
| Admin | admin@cps.com | password |

> To add more students, run the SQL in phpMyAdmin:
> ```sql
> INSERT INTO users (name, email, password, role, student_id) VALUES
> ('Student Name', 'email@cps.com', 
> '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', 
> 'student', 'STU002');
> ```

---

## API Endpoints

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | /api/login.php | None | Login and get JWT token |
| POST | /api/attendance_post.php | Teacher | Sync attendance records |
| GET | /api/attendance_get.php | Any | Get attendance records |
| GET | /api/statistics.php | Any | Get dashboard statistics |

---

## References

- [OkHttp3](https://square.github.io/okhttp/) — HTTP client for Android
- [Chart.js](https://www.chartjs.org/) — JavaScript charts for dashboard
- [Room Database](https://developer.android.com/training/data-storage/room) — Local SQLite ORM
- [Android HCE Guide](https://developer.android.com/guide/topics/connectivity/nfc/hce) — Host Card Emulation
- [XAMPP](https://www.apachefriends.org) — Local PHP/MySQL server
