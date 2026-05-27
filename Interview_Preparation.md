# 🎯 Sree Neha Events — Interview Preparation Guide

---

## 📌 1. Project Overview

**Project Name:** Sree Neha Events

**Type:** Full-Stack Web Application

**Purpose:**
Sree Neha Events is an online event management platform designed to showcase premium event services like weddings, birthdays, and corporate events. It allows visitors to browse services with detailed information and lets the admin manage the entire content — services, images, and banners — through a secure admin dashboard.

**Live URLs:**
- 🌐 Frontend: https://sreenehaevents.vercel.app
- 🖥️ Backend API: https://sree-neha-events-backend.onrender.com

---

## 🛠️ 2. Tech Stack

### Frontend
| Technology | Why Used |
|---|---|
| React 18 | Component-based UI development |
| Vite | Fast build tool and dev server |
| Tailwind CSS | Responsive, utility-first styling |
| Framer Motion | Smooth animations and page transitions |
| Axios | HTTP API requests to backend |
| Lucide React | Clean icon library |
| React Router DOM | Client-side routing |

### Backend
| Technology | Why Used |
|---|---|
| Node.js + Express | RESTful API server |
| MongoDB + Mongoose | Database and schema management |
| Cloudinary | Cloud image storage |
| Multer + multer-storage-cloudinary | File upload middleware |
| Brevo API | Transactional OTP email delivery |
| JSON Web Tokens (JWT) | Secure admin authentication |
| Bcryptjs | OTP hashing for security |
| dotenv | Environment variable management |

### Hosting
| Service | Purpose |
|---|---|
| Vercel | Frontend hosting |
| Render | Backend hosting |
| MongoDB Atlas | Cloud database |
| Cloudinary | Image CDN storage |

---

## 🏗️ 3. Project Architecture

```
User (Browser)
     │
     ▼
React SPA (Vercel)
     │  Axios HTTP Requests
     ▼
Express REST API (Render)
     │
     ├── Auth Routes (/api/auth)
     │       └── Brevo API → OTP Email
     │
     ├── Service Routes (/api/services)
     │       └── Cloudinary → Image Storage
     │
     └── Banner Routes (/api/banners)
             └── Cloudinary → Image Storage
                     │
                     ▼
               MongoDB Atlas
               (Cloud Database)
```

**How it works:**
- Frontend is deployed separately on Vercel
- Backend API runs on Render
- Images are stored on Cloudinary (not on server disk)
- Database is hosted on MongoDB Atlas cloud

---

## ✨ 4. Key Features

### For Visitors (Public Users):
1. **Homepage Banner Carousel** — Auto-sliding images managed by admin
2. **Service Listing** — Grid view of all event services with category filter
3. **Service Detail Modal** — Full details: price, duration, location, description, YouTube videos, terms
4. **About Us Page** — Company info, team, mission and values
5. **Contact Info** — Phone number, email, location always visible

### For Admin (Protected):
1. **OTP Login** — Secure passwordless login via email OTP
2. **Manage Services** — Add, Edit, Delete event services with images
3. **Manage Banners** — Upload/Delete homepage banner images
4. **Image Upload** — Direct cloud upload via Cloudinary
5. **Auto Logout** — JWT token expires after 24 hours

---

## 🔐 5. Authentication Flow (OTP Login)

**Step-by-step:**

```
1. Admin visits /login page
2. Enters registered email address
3. Frontend calls POST /api/auth/send-otp
4. Backend checks if email exists in database
5. Generates a random 6-digit OTP
6. Hashes the OTP using bcryptjs (for security)
7. Saves hashed OTP + expiry time (10 minutes) in DB
8. Sends plain OTP to admin email via Brevo API
9. Admin enters OTP on login page
10. Frontend calls POST /api/auth/verify-otp
11. Backend compares entered OTP with stored hash
12. If valid → clears OTP from DB → issues JWT token
13. Frontend stores JWT in localStorage
14. All admin API calls include: Authorization: Bearer <token>
```

**Why OTP instead of password?**
- Only one admin → no need for complex password flows
- More secure → no password to steal or forget
- No password reset flows needed
- bcryptjs hashing means even if DB is compromised, OTP is safe

---

## 🖼️ 6. Image Upload Flow (Cloudinary)

**Step-by-step:**

```
1. Admin selects image in dashboard
2. Frontend sends multipart/form-data with JWT token
3. Multer middleware on backend intercepts the file
4. multer-storage-cloudinary streams it directly to Cloudinary
5. Cloudinary returns:
   - imageUrl (permanent CDN URL)
   - publicId (unique identifier for deletion)
6. Backend saves both imageUrl and publicId in MongoDB
7. Image is now served from Cloudinary CDN globally
8. When admin deletes service/banner:
   - Backend calls cloudinary.uploader.destroy(publicId)
   - Then removes record from MongoDB
```

**Why Cloudinary instead of server disk?**
- Render (free tier) has ephemeral filesystem
- Files stored on disk get deleted on every server restart
- Cloudinary keeps images permanently on the cloud
- CDN delivery makes images load faster globally

---

## 🗃️ 7. Database Schemas

### User (Admin)
```javascript
{
  email:      String (required, unique),  // Admin email
  otp:        String,                     // Bcrypt hashed OTP
  otpExpires: Date                        // OTP expiry (10 min)
}
```

### Service
```javascript
{
  title:       String (required),  // Service name
  image:       String,             // Cloudinary CDN URL
  publicId:    String,             // For Cloudinary deletion
  category:    String,             // Birthday, Wedding, etc.
  price:       String,             // e.g., ₹15,000
  duration:    String,             // e.g., 4 hours
  location:    String,             // Where service is offered
  youtubeLink: String,             // Promo video link
  description: String,             // Full service details
  terms:       String,             // Terms and conditions
  fromYourEnd: String              // What client needs to arrange
}
```

### Banner
```javascript
{
  imageUrl:  String (required),    // Cloudinary CDN URL
  publicId:  String,               // For Cloudinary deletion
  isActive:  Boolean (default: true),
  createdAt: Date
}
```

---

## 📡 8. API Endpoints

### Auth Routes
| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| POST | /api/auth/send-otp | ❌ No | Send OTP to admin email |
| POST | /api/auth/verify-otp | ❌ No | Verify OTP, get JWT token |

### Service Routes
| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| GET | /api/services | ❌ No | Get all services |
| GET | /api/services/:id | ❌ No | Get one service |
| POST | /api/services | ✅ Yes | Add new service |
| PUT | /api/services/:id | ✅ Yes | Update service |
| DELETE | /api/services/:id | ✅ Yes | Delete service |

### Banner Routes
| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| GET | /api/banners | ❌ No | Get all banners |
| POST | /api/banners | ✅ Yes | Upload new banner |
| DELETE | /api/banners/:id | ✅ Yes | Delete banner |

---

## 🔒 9. Security Implementation

1. **OTP Hashing** — bcryptjs salts and hashes OTP before saving to DB
2. **OTP Expiry** — Auto-expires after 10 minutes
3. **JWT Middleware** — All admin routes verify token signature before proceeding
4. **CORS Policy** — Only allows requests from the deployed frontend URL and localhost
5. **Protected Routes** — Frontend ProtectedRoute.jsx redirects unauthenticated users to login
6. **No Password Storage** — Zero risk of password-related breaches

---

## 🚀 10. Deployment Setup

### Frontend → Vercel
- Framework: Vite (React)
- Build Command: `npm run build`
- Output: `dist/` folder
- Auto-deploys from GitHub

### Backend → Render
- Runtime: Node.js
- Start Command: `node index.js`
- All environment variables added in Render dashboard
- Free tier (may cold-start after inactivity)

### Environment Variables (Backend)
```
MONGODB_URI            → MongoDB Atlas connection string
JWT_SECRET             → Secret key for JWT signing
BREVO_API_KEY          → Brevo email API key
BREVO_USER             → Sender email address
CLOUDINARY_CLOUD_NAME
CLOUDINARY_API_KEY
CLOUDINARY_API_SECRET
```

---

## ⚡ 11. Challenges Faced & Solutions

### Challenge 1: Images deleted after server restart
**Problem:** When deploying on Render, any images saved to the server disk were wiped out on restart.
**Solution:** Integrated multer-storage-cloudinary to stream uploads directly to Cloudinary cloud. Server disk is never used.

### Challenge 2: OTP emails not delivering
**Problem:** Gmail SMTP ports (465/587) were blocked by Render's hosting environment.
**Solution:** Switched to Brevo HTTP REST API which uses port 443 (HTTPS) — never blocked by firewalls. 100% reliable delivery.

### Challenge 3: Admin access security
**Problem:** Simple password login is easy to brute-force or forget.
**Solution:** Implemented OTP-based passwordless login. OTPs are bcrypt-hashed, time-limited (10 min), and single-use.

### Challenge 4: CORS errors between frontend and backend
**Problem:** API calls from Vercel frontend were blocked by CORS.
**Solution:** Configured explicit allowedOrigins list in Express CORS middleware, allowing only the Vercel URL and localhost.

---

## ❓ 12. Common Interview Questions & Answers

### Q1: Explain your project in 2-3 lines.
**A:** Sree Neha Events is a full-stack event management web application where visitors can browse event services like weddings and birthdays, and the admin can securely manage all content — services, pricing, images, and banners — through a dedicated dashboard protected by OTP-based JWT authentication.

---

### Q2: Why did you use JWT for authentication?
**A:** JWT (JSON Web Tokens) are stateless, meaning the server doesn't need to store session data in a database. The token is signed with a secret key, so it's tamper-proof. The admin's token is stored in localStorage and sent as a Bearer header with every admin API request.

---

### Q3: What is bcryptjs and why did you use it?
**A:** bcryptjs is a password hashing library. Even though we use OTPs instead of passwords, we still hash the OTP before saving it to the database. This means even if someone accesses the database directly, they cannot read the original OTP. When verifying, we use bcrypt.compare() to match the entered OTP against the stored hash.

---

### Q4: How does Cloudinary integration work?
**A:** We use multer-storage-cloudinary as a storage engine for Multer. When an admin uploads an image, instead of saving it to the server's disk, Multer automatically streams the file to Cloudinary. Cloudinary returns a permanent CDN URL and a publicId. We save both in MongoDB — the URL for displaying the image, and the publicId for deleting it later when the service is removed.

---

### Q5: What is CORS and how did you handle it?
**A:** CORS (Cross-Origin Resource Sharing) is a browser security feature that blocks API calls from a different domain by default. Since our frontend (Vercel) and backend (Render) are on different domains, we configured Express CORS middleware to explicitly allow only our Vercel URL and localhost. Any other origin is rejected automatically.

---

### Q6: What is the difference between Vercel and Render?
**A:** Vercel is optimized for frontend hosting — it auto-deploys static sites and SPAs from GitHub very efficiently. Render is used for backend Node.js servers because it supports running persistent server processes. Vercel only hosts static files; it cannot run a continuous Express server.

---

### Q7: Why did you use React Context API instead of Redux?
**A:** For this project, only authentication state (user login info and token) needed to be shared globally. React Context API is perfectly sufficient for this use case. Redux would add unnecessary complexity and boilerplate for such a simple, single-concern global state.

---

### Q8: What is Framer Motion and how did you use it?
**A:** Framer Motion is a React animation library. We used it for smooth page transition animations using AnimatePresence, hover effects on buttons and cards, and animated dropdown mobile menu. It makes the UI feel premium and professional without writing complex CSS keyframe animations.

---

### Q9: How do you handle image deletion properly?
**A:** When an admin deletes a service, the backend first calls cloudinary.uploader.destroy(publicId) to remove the image from Cloudinary cloud storage. Then it deletes the document from MongoDB. This two-step cleanup ensures no orphaned images remain in Cloudinary taking up storage.

---

### Q10: What would you improve if you had more time?
**A:**
1. Add pagination for services list when services grow large
2. Add image optimization (resize/compress before upload)
3. Implement refresh token mechanism so admin does not need to re-login every 24 hours
4. Add analytics dashboard to track page views and popular services
5. Add a booking/enquiry form so visitors can directly request services

---

## 📋 13. Resume Points (Ready to Copy)

**Project Name:** Sree Neha Events | Full-Stack Web Application

**Points:**
1. Built a full-stack event management web app using React.js, Node.js, Express.js, and MongoDB for managing and showcasing premium event services.
2. Implemented secure OTP-based authentication with JWT tokens and bcryptjs hashing, delivering OTPs via Brevo HTTP API for firewall-safe email delivery.
3. Integrated Cloudinary cloud storage using multer-storage-cloudinary to stream images directly to the cloud, ensuring zero server disk dependency.
4. Developed a responsive Admin Dashboard with full CRUD operations for services, pricing, descriptions, and dynamic homepage banner management.
5. Designed a modern UI with Tailwind CSS and Framer Motion featuring animated transitions, category-based filtering, and a dynamic auto-sliding banner carousel.

---

*Deployed on Vercel (Frontend) + Render (Backend) | GitHub: github.com/lvs170603*

---

## 🐙 14. Git Commands (Version Control)

### 🔧 Git Setup (First Time Only)
```bash
# Set your name and email globally
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# Check current config
git config --list
```

---

### 📁 Starting a New Project
```bash
# Initialize a new git repository
git init

# Clone an existing repository from GitHub
git clone https://github.com/lvs170603/Sree_neha_events_Frontend.git

# Clone backend repo
git clone https://github.com/lvs170603/Sree_neha_events_Backend.git
```

---

### 📝 Daily Workflow Commands
```bash
# Check status of files (what is changed/staged/untracked)
git status

# Add a specific file to staging area
git add filename.js

# Add all changed files to staging area
git add .

# Commit with a message
git commit -m "Add new service feature"

# Push to GitHub (main branch)
git push origin main

# Pull latest changes from GitHub
git pull origin main
```

---

### 🌿 Branch Commands
```bash
# See all branches
git branch

# Create a new branch
git branch feature/admin-dashboard

# Switch to a branch
git checkout feature/admin-dashboard

# Create and switch in one command
git checkout -b feature/new-feature

# Merge a branch into main
git checkout main
git merge feature/admin-dashboard

# Delete a branch (after merge)
git branch -d feature/admin-dashboard

# Push a new branch to GitHub
git push origin feature/admin-dashboard
```

---

### 🔍 Viewing History
```bash
# View commit history
git log

# View compact commit history (one line each)
git log --oneline

# View changes in a file
git diff filename.js

# View staged changes
git diff --staged
```

---

### ↩️ Undo / Fix Mistakes
```bash
# Unstage a file (undo git add)
git restore --staged filename.js

# Discard changes in working directory (undo edits)
git restore filename.js

# Undo last commit (keep changes in files)
git reset --soft HEAD~1

# Undo last commit (discard changes too) ⚠️ DANGER
git reset --hard HEAD~1

# Revert a specific commit (safe way)
git revert <commit-hash>
```

---

### 🔗 Remote Repository Commands
```bash
# Check connected remote URLs
git remote -v

# Add a remote origin
git remote add origin https://github.com/lvs170603/repo-name.git

# Change remote URL
git remote set-url origin https://github.com/lvs170603/new-repo.git

# Remove remote
git remote remove origin
```

---

### 🏷️ Tags (for versions/releases)
```bash
# Create a tag
git tag v1.0.0

# Push tags to GitHub
git push origin --tags

# List all tags
git tag
```

---

### 📌 This Project — Git Commands Used
```bash
# Frontend Project
cd client
git init
git add .
git commit -m "Initial React frontend setup"
git remote add origin https://github.com/lvs170603/Sree_neha_events_Frontend.git
git push -u origin main

# Backend Project
cd server
git init
git add .
git commit -m "Initial Express backend setup"
git remote add origin https://github.com/lvs170603/Sree_neha_events_Backend.git
git push -u origin main

# After making changes (daily push)
git add .
git commit -m "Add Cloudinary image upload feature"
git push origin main
```

---

### ❓ Common Git Interview Questions & Answers

**Q: What is the difference between git pull and git fetch?**
A: `git fetch` downloads changes from remote but does NOT merge them. `git pull` = `git fetch` + `git merge`. Use fetch to preview changes before merging.

**Q: What is the difference between git merge and git rebase?**
A: `git merge` creates a new merge commit combining two branches. `git rebase` moves commits on top of another branch — cleaner history but rewrites commits. Merge is safer for team projects.

**Q: What is .gitignore?**
A: A file that tells Git which files/folders to ignore and NOT track. Example: `node_modules/`, `.env` files with secrets, `dist/` build folders.

**Q: What is git stash?**
A: Temporarily saves uncommitted changes so you can switch branches without committing. Use `git stash` to save, `git stash pop` to restore.

**Q: What is the difference between git reset and git revert?**
A: `git reset` removes commits from history (dangerous in shared repos). `git revert` creates a new commit that undoes changes — safe for shared/public repos.

---

*Deployed on Vercel (Frontend) + Render (Backend) | GitHub: github.com/lvs170603*
