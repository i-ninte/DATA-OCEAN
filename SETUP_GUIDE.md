# Data Ocean - Complete Setup Guide

This guide will walk you through setting up the complete Data Ocean application with backend API and frontend integration.

## Prerequisites

- Python 3.8 or higher
- MySQL or MariaDB installed and running
- Gmail account with App Password (for SMTP)
- Modern web browser
- Git (optional)

## Step 1: Database Setup

### 1.1 Install MySQL/MariaDB

**macOS:**
```bash
brew install mysql
brew services start mysql
```

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install mysql-server
sudo systemctl start mysql
```

**Windows:**
Download and install from [MySQL Downloads](https://dev.mysql.com/downloads/mysql/)

### 1.2 Create Database

```bash
# Login to MySQL
mysql -u root -p

# Create database
CREATE DATABASE dataocean CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

# Create user (optional but recommended)
CREATE USER 'dataocean_user'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON dataocean.* TO 'dataocean_user'@'localhost';
FLUSH PRIVILEGES;

# Exit
EXIT;
```

## Step 2: Backend Setup

### 2.1 Navigate to Backend Directory

```bash
cd backend
```

### 2.2 Run Setup Script

```bash
chmod +x setup.sh
./setup.sh
```

Or manually:

```bash
# Create virtual environment
python3 -m venv venv

# Activate virtual environment
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 2.3 Configure Environment Variables

Edit the `.env` file with your actual credentials:

```env
# Database Configuration
DB_HOST=localhost
DB_PORT=3306
DB_USER=your_db_user
DB_PASSWORD=your_db_password
DB_NAME=dataocean

# SMTP Configuration (Gmail)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=info@tdoh.io
SMTP_PASSWORD=your_gmail_app_password
SMTP_FROM_EMAIL=info@tdoh.io
SMTP_FROM_NAME=Data Ocean

# Notification Emails
NOTIFICATION_EMAILS=max@maxwellababio.com,info@tdoh.io

# Application Settings
APP_ENV=development
DEBUG=True
CORS_ORIGINS=http://localhost:3000,http://localhost:8000,http://127.0.0.1:5500,http://127.0.0.1:8080
```

**Important Notes:**

1. **Gmail App Password**:
   - Go to Google Account → Security → 2-Step Verification → App Passwords
   - Generate a new app password for "Mail"
   - Use this password in `SMTP_PASSWORD`

2. **Database Credentials**:
   - Replace `your_db_user` and `your_db_password` with your actual MySQL credentials
   - If using root user, update `DB_USER=root` and use your root password

### 2.4 Run Database Migrations

```bash
# Generate initial migration
alembic revision --autogenerate -m "Initial migration"

# Apply migrations
alembic upgrade head
```

### 2.5 Seed Questions

```bash
python seed_questions.py
```

You should see: `Successfully seeded X questions!`

### 2.6 Start Backend Server

```bash
# Development mode (with auto-reload)
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at:
- API: http://localhost:8000
- Documentation: http://localhost:8000/docs
- Alternative Docs: http://localhost:8000/redoc

## Step 3: Frontend Setup

### 3.1 Update API Endpoint (if needed)

If you're running the backend on a different host/port, update the API endpoints in `index.html`:

Find and replace `http://localhost:8000` with your backend URL.

### 3.2 Serve Frontend

You can use any static file server. Here are some options:

**Option 1: Python HTTP Server**
```bash
# From the project root
python3 -m http.server 8080
```

**Option 2: Live Server (VS Code)**
- Install "Live Server" extension in VS Code
- Right-click `index.html` → "Open with Live Server"

**Option 3: Node.js http-server**
```bash
npm install -g http-server
http-server -p 8080
```

The frontend will be available at: http://localhost:8080

## Step 4: Testing the Integration

### 4.1 Test Backend API

Visit http://localhost:8000/docs and test the endpoints:

1. **GET /api/questions/ai_readiness** - Should return questions
2. **GET /api/questions/data_valuation** - Should return questions
3. **GET /health** - Should return `{"status": "healthy"}`

### 4.2 Test Frontend Features

1. **Get Started Flow**:
   - Click "Get Started" button
   - Select a service (AI Readiness, Data Valuation, or Data Monetization)
   - Answer all questions
   - Submit and check your email

2. **Contact Form**:
   - Scroll to "Let's Talk Data" section
   - Fill out the form
   - Submit and check your email

3. **Schedule Meeting**:
   - Click "Schedule A Meeting" in the hero section
   - Fill out meeting details
   - Submit and check your email for Google Meet link

### 4.3 Verify Email Delivery

Check these inboxes:
- User's email (the one entered in forms)
- max@maxwellababio.com
- info@tdoh.io

You should receive:
- Submission confirmations
- Contact form notifications
- Meeting schedules with Google Meet links

## Step 5: Troubleshooting

### Backend Won't Start

**Error: "Can't connect to MySQL server"**
- Verify MySQL is running: `sudo service mysql status`
- Check credentials in `.env`
- Ensure database exists: `SHOW DATABASES;`

**Error: "No module named 'app'"**
- Activate virtual environment: `source venv/bin/activate`
- Reinstall dependencies: `pip install -r requirements.txt`

### Frontend Can't Connect to Backend

**Error: "Failed to fetch" or CORS errors**
- Verify backend is running: http://localhost:8000/health
- Check CORS_ORIGINS in `.env` includes your frontend URL
- Clear browser cache and reload

### Email Not Sending

**Error: "SMTP authentication failed"**
- Verify you're using an App Password, not your regular Gmail password
- Check Gmail security settings
- Ensure 2-Step Verification is enabled

**Emails not received:**
- Check spam/junk folder
- Verify SMTP credentials in `.env`
- Check backend logs for errors

### Database Migration Issues

```bash
# Reset migrations
alembic downgrade base
alembic upgrade head

# If that doesn't work, delete migration files and start fresh
rm alembic/versions/*.py
alembic revision --autogenerate -m "Initial migration"
alembic upgrade head
```

## Step 6: Production Deployment

### 6.1 Update Environment

```env
APP_ENV=production
DEBUG=False
CORS_ORIGINS=https://yourdomain.com
```

### 6.2 Use Production Server

```bash
# Install gunicorn
pip install gunicorn

# Run with gunicorn
gunicorn app.main:app --workers 4 --worker-class uvicorn.workers.UvicornWorker --bind 0.0.0.0:8000
```

### 6.3 Set Up Reverse Proxy (Nginx)

Example Nginx configuration:

```nginx
server {
    listen 80;
    server_name api.yourdomain.com;

    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

server {
    listen 80;
    server_name yourdomain.com;

    root /path/to/DATA OCEAN;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### 6.4 Enable SSL

```bash
# Install certbot
sudo apt install certbot python3-certbot-nginx

# Get SSL certificate
sudo certbot --nginx -d yourdomain.com -d api.yourdomain.com
```

## Google Meet Integration (Optional)

To enable full Google Meet functionality:

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project
3. Enable Google Calendar API
4. Create OAuth 2.0 credentials
5. Add authorized redirect URIs
6. Update `.env` with credentials:
   ```env
   GOOGLE_CLIENT_ID=your_client_id_here
   GOOGLE_CLIENT_SECRET=your_client_secret_here
   GOOGLE_REDIRECT_URI=http://localhost:8000/auth/callback
   ```

## Database Backup

```bash
# Backup database
mysqldump -u root -p dataocean > backup_$(date +%Y%m%d).sql

# Restore database
mysql -u root -p dataocean < backup_20240101.sql
```

## Monitoring and Logs

```bash
# View backend logs
tail -f logs/app.log

# Monitor database queries
mysql -u root -p -e "SHOW PROCESSLIST;"
```

## Support

For issues or questions:
- Email: info@tdoh.io
- Check API docs: http://localhost:8000/docs
- Review backend logs

## Summary

You now have:
- ✅ FastAPI backend with database integration
- ✅ MySQL database with questions and submissions tracking
- ✅ SMTP email notifications
- ✅ Google Meet scheduling (placeholder)
- ✅ Frontend integrated with backend APIs
- ✅ Contact form with email notifications
- ✅ Meeting scheduler with automated emails

All form submissions, questions, and answers are now stored in your local database and trigger email notifications to both users and admins!
