# Data Ocean - Quick Start Guide

## Prerequisites Check

Before starting, ensure you have:
- [ ] Python 3.8+ installed
- [ ] MySQL/MariaDB running
- [ ] Gmail App Password ready
- [ ] Database created: `dataocean`

## 5-Minute Setup

### 1. Backend Setup (2 minutes)

```bash
cd backend

# Setup environment
./setup.sh

# Or manually:
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Configure .env with your credentials
# Edit: DB_USER, DB_PASSWORD, SMTP_PASSWORD

# Run migrations
alembic upgrade head

# Seed questions
python seed_questions.py

# Start server
uvicorn app.main:app --reload
```

Backend will be at: http://localhost:8000

### 2. Frontend Setup (1 minute)

```bash
# From project root
python3 -m http.server 8080
```

Frontend will be at: http://localhost:8080

### 3. Test (2 minutes)

1. Open http://localhost:8080
2. Click "Get Started"
3. Select "AI Readiness"
4. Fill out the form
5. Check emails at:
   - max@maxwellababio.com
   - info@tdoh.io
   - User's email

## Key Features

### ✅ What's Working

1. **Question Management**
   - Questions stored in database
   - Three service types: AI Readiness, Data Valuation, Data Monetization
   - Dynamic form rendering

2. **Email Notifications**
   - User confirmation emails
   - Admin notifications to max@maxwellababio.com and info@tdoh.io
   - Professional HTML templates

3. **Contact Form**
   - Stores submissions in database
   - Sends emails to both parties

4. **Meeting Scheduler**
   - Schedules meetings with date/time
   - Creates Google Meet link (placeholder)
   - Sends meeting details via email

5. **Database Tracking**
   - All submissions tracked
   - User management
   - Answer history

## API Endpoints

### Questions
- `GET /api/questions/` - All questions
- `GET /api/questions/{service_type}` - Questions by service

### Submissions
- `POST /api/submissions/` - Submit answers
- `GET /api/submissions/{id}` - Get submission

### Contacts
- `POST /api/contacts/` - Contact form

### Meetings
- `POST /api/meetings/` - Schedule meeting

## Configuration Files

### Backend `.env`
```env
DB_HOST=localhost
DB_USER=your_user
DB_PASSWORD=your_password
DB_NAME=dataocean

SMTP_USER=info@tdoh.io
SMTP_PASSWORD=your_app_password

NOTIFICATION_EMAILS=max@maxwellababio.com,info@tdoh.io
```

### Database Connection String
```
mysql+pymysql://user:password@localhost:3306/dataocean
```

## Common Commands

### Backend

```bash
# Start development server
uvicorn app.main:app --reload

# Create migration
alembic revision --autogenerate -m "migration name"

# Apply migrations
alembic upgrade head

# Rollback migration
alembic downgrade -1

# Seed questions
python seed_questions.py

# Check API docs
open http://localhost:8000/docs
```

### Database

```bash
# Connect to database
mysql -u root -p dataocean

# View tables
SHOW TABLES;

# View questions
SELECT * FROM questions;

# View submissions
SELECT * FROM submissions;

# View users
SELECT * FROM users;
```

## Testing Checklist

- [ ] Backend starts without errors
- [ ] Can access http://localhost:8000/docs
- [ ] Can fetch questions: `GET /api/questions/ai_readiness`
- [ ] Frontend loads at http://localhost:8080
- [ ] "Get Started" opens modal
- [ ] Questions load from backend
- [ ] Can submit answers
- [ ] Emails are received
- [ ] Contact form works
- [ ] Meeting scheduler works

## Troubleshooting

### Backend won't start
```bash
# Check MySQL is running
sudo service mysql status

# Test database connection
mysql -u root -p -e "USE dataocean; SHOW TABLES;"

# Reinstall dependencies
pip install --force-reinstall -r requirements.txt
```

### Frontend can't connect
```bash
# Check CORS settings in .env
CORS_ORIGINS=http://localhost:8080

# Restart backend
pkill -f uvicorn
uvicorn app.main:app --reload
```

### Emails not sending
```bash
# Test SMTP connection
python -c "import smtplib; s=smtplib.SMTP('smtp.gmail.com',587); s.starttls(); s.login('info@tdoh.io', 'your_app_password'); print('Success!')"
```

## Project Structure

```
DATA OCEAN/
├── backend/
│   ├── alembic/              # Database migrations
│   ├── app/
│   │   ├── routers/          # API endpoints
│   │   ├── config.py         # Settings
│   │   ├── database.py       # DB connection
│   │   ├── models.py         # Database models
│   │   ├── schemas.py        # Request/response schemas
│   │   ├── email_service.py  # Email functionality
│   │   ├── google_meet_service.py  # Meeting scheduler
│   │   └── main.py           # FastAPI app
│   ├── .env                  # Environment variables
│   ├── requirements.txt      # Python dependencies
│   └── seed_questions.py     # Database seeding
├── index.html                # Frontend
├── SETUP_GUIDE.md           # Full setup instructions
└── QUICK_START.md           # This file
```

## Next Steps

1. **Update Email Password**
   - Generate Gmail App Password
   - Update `SMTP_PASSWORD` in `.env`

2. **Test All Features**
   - Submit forms
   - Check email delivery
   - Verify database entries

3. **Customize Questions**
   - Edit `seed_questions.py`
   - Re-run to update database

4. **Deploy to Production**
   - See SETUP_GUIDE.md for deployment instructions
   - Set up SSL certificates
   - Use production database

## Support

- API Documentation: http://localhost:8000/docs
- Email: info@tdoh.io
- Check logs: backend logs show detailed error messages

## Success Indicators

You've successfully set up everything when:
- ✅ Backend running on port 8000
- ✅ Frontend running on port 8080
- ✅ Can view API docs
- ✅ Questions load in frontend
- ✅ Forms submit successfully
- ✅ Emails are delivered
- ✅ Database stores all submissions

Congratulations! Your Data Ocean application is now fully functional with backend integration! 🎉
