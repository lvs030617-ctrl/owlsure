# 🎯 Sree Neha Events — Interview Preparation Guide (Roman Telugu)

---

## 📌 1. Project Overview

**Project Peru:** Sree Neha Events

**Type:** Full-Stack Web Application

**Project Gurinchi:**
Sree Neha Events ante oka online event management platform. Dantilo weddings, birthdays, corporate events lanti premium services ni visitors browse cheyagalaru. Admin matram secure dashboard dwara anni content ni — services, images, banners — manage cheskovachu.

**Live URLs:**
- 🌐 Frontend: https://sreenehaevents.vercel.app
- 🖥️ Backend API: https://sree-neha-events-backend.onrender.com

---

## 🛠️ 2. Tech Stack

### Frontend (User Side)
| Technology | Enduku Vadamu |
|---|---|
| React 18 | Components tho UI build chesam |
| Vite | Fast ga build cheyyadaniki |
| Tailwind CSS | Mobile ki kuda responsive ayye styling kosam |
| Framer Motion | Smooth animations kosam |
| Axios | Backend ki HTTP requests pampadam kosam |
| Lucide React | Icons kosam |
| React Router DOM | Pages madhya navigate avvadam kosam |

### Backend (Server Side)
| Technology | Enduku Vadamu |
|---|---|
| Node.js + Express | REST API server run cheyyadaniki |
| MongoDB + Mongoose | Database manage cheyyadaniki |
| Cloudinary | Cloud lo images store cheyyadaniki |
| Multer + multer-storage-cloudinary | File upload cheyyadaniki |
| Brevo API | OTP email pampadam kosam |
| JWT (JSON Web Token) | Admin login secure ga cheyyadaniki |
| Bcryptjs | OTP ni hash cheyyadaniki (security kosam) |
| dotenv | Environment variables manage cheyyadaniki |

### Hosting
| Service | Enduku |
|---|---|
| Vercel | Frontend host cheyyadaniki |
| Render | Backend host cheyyadaniki |
| MongoDB Atlas | Cloud database kosam |
| Cloudinary | Images cloud lo store avvadam kosam |

---

## 🏗️ 3. Project Architecture (System Ela Pani Chestundi)

```
User (Browser)
     │
     ▼
React SPA (Vercel lo deployed)
     │  Axios tho HTTP Requests pampistundi
     ▼
Express REST API (Render lo run avutundi)
     │
     ├── Auth Routes (/api/auth)
     │       └── Brevo API → OTP Email pampistundi
     │
     ├── Service Routes (/api/services)
     │       └── Cloudinary → Image store avutundi
     │
     └── Banner Routes (/api/banners)
             └── Cloudinary → Image store avutundi
                     │
                     ▼
               MongoDB Atlas
               (Cloud Database lo save avutundi)
```

**Summary:**
- Frontend Vercel lo undi, Backend Render lo undi
- Images Cloudinary lo store avutayi (server disk lo kaadu)
- Database MongoDB Atlas cloud lo undi

---

## ✨ 4. Key Features (Mukhyamaina Features)

### Visitors Kosam (Public Users):
1. **Homepage Banner Carousel** — Admin upload chesina images automatic ga slide avutayi
2. **Service Listing** — Anni event services grid lo chupistundi, category filter kuda undi
3. **Service Detail Modal** — Price, duration, location, description, YouTube video, terms anni chupistundi
4. **About Us Page** — Company info, team, mission anni chupistundi
5. **Contact Info** — Phone number, email, location ekkada chusina kantabadatayi

### Admin Kosam (Protected - JWT tho secure):
1. **OTP Login** — Email ki OTP vastuundi, password avasaram ledu
2. **Manage Services** — New service add, edit, delete cheyachu
3. **Manage Banners** — Homepage banner images upload/delete cheyachu
4. **Image Upload** — Direct ga Cloudinary ki upload avutundi
5. **Auto Logout** — 24 hours tarvata JWT token expire avutundi

---

## 🔐 5. Authentication Flow (OTP Login Ela Pani Chestundi)

**Step by Step:**

```
1. Admin /login page ki vastadu
2. Registered email enter chestadu
3. Frontend POST /api/auth/send-otp call chestundi
4. Backend aa email database lo unda check chestundi
5. 6-digit random OTP generate chestundi
6. Aa OTP ni bcryptjs tho hash chestundi (security kosam)
7. Hashed OTP + expiry time (10 minutes) DB lo save chestundi
8. Plain OTP ni admin email ki Brevo API dwara pampistundi
9. Admin OTP enter chestadu login page lo
10. Frontend POST /api/auth/verify-otp call chestundi
11. Backend entered OTP ni stored hash tho compare chestundi
12. Valid aithe → DB lo OTP clear chestundi → JWT token istundi
13. Frontend JWT ni localStorage lo store chestundi
14. Anni admin API calls lo: Authorization: Bearer <token> pampistundi
```

**OTP login enduku password kaadu?**
- Okke admin undi — complex password flows avasaram ledu
- More secure — password donga poku, marchipoyu risk ledu
- Password reset flow avasaram ledu
- bcryptjs hashing valla DB compromise ainappatiki kuda OTP safe ga untundi

---

## 🖼️ 6. Image Upload Flow (Cloudinary Ela Pani Chestundi)

**Step by Step:**

```
1. Admin dashboard lo image select chestadu
2. Frontend multipart/form-data ni JWT token tho pampistundi
3. Backend lo Multer middleware file ni intercept chestundi
4. multer-storage-cloudinary direct ga Cloudinary ki stream chestundi
5. Cloudinary rendu things return chestundi:
   - imageUrl (permanent CDN URL)
   - publicId (delete cheyyadaniki unique ID)
6. Backend rendu ni MongoDB lo save chestundi
7. Image ippudu Cloudinary CDN nundi globally serve avutundi
8. Admin service/banner delete chestinapudu:
   - Backend cloudinary.uploader.destroy(publicId) call chestundi
   - Tarvata MongoDB lo record delete chestundi
```

**Server disk kaadu, Cloudinary enduku?**
- Render free tier lo ephemeral filesystem undi
- Server disk lo save chesthe server restart ainapudu files delete avutayi
- Cloudinary lo images permanently untayi
- CDN valla images faster ga load avutayi

---

## 🗃️ 7. Database Schemas (Database Structure)

### User (Admin)
```javascript
{
  email:      String (required, unique),  // Admin email
  otp:        String,                     // Bcrypt hash chesina OTP
  otpExpires: Date                        // OTP expire time (10 min)
}
```

### Service (Event Services)
```javascript
{
  title:       String (required),  // Service peru
  image:       String,             // Cloudinary CDN URL
  publicId:    String,             // Cloudinary lo delete cheyyadaniki
  category:    String,             // Birthday, Wedding, etc.
  price:       String,             // Ex: Rs.15,000
  duration:    String,             // Ex: 4 hours
  location:    String,             // Service ekkada istaaru
  youtubeLink: String,             // Promo video link
  description: String,             // Full service details
  terms:       String,             // Terms and conditions
  fromYourEnd: String              // Client arrange chesukovalsinavi
}
```

### Banner (Homepage Images)
```javascript
{
  imageUrl:  String (required),    // Cloudinary CDN URL
  publicId:  String,               // Cloudinary lo delete cheyyadaniki
  isActive:  Boolean (default: true),
  createdAt: Date
}
```

---

## 📡 8. API Endpoints (Routes Table)

### Auth Routes
| Method | Endpoint | Auth Kavala? | Chese Pani |
|---|---|---|---|
| POST | /api/auth/send-otp | ❌ Vaddu | Admin email ki OTP pampinchu |
| POST | /api/auth/verify-otp | ❌ Vaddu | OTP verify chesi JWT token ivvu |

### Service Routes
| Method | Endpoint | Auth Kavala? | Chese Pani |
|---|---|---|---|
| GET | /api/services | ❌ Vaddu | Anni services teechuko |
| GET | /api/services/:id | ❌ Vaddu | Oka service teechuko |
| POST | /api/services | ✅ Kavali | New service add cheyyi |
| PUT | /api/services/:id | ✅ Kavali | Service update cheyyi |
| DELETE | /api/services/:id | ✅ Kavali | Service delete cheyyi |

### Banner Routes
| Method | Endpoint | Auth Kavala? | Chese Pani |
|---|---|---|---|
| GET | /api/banners | ❌ Vaddu | Anni banners teechuko |
| POST | /api/banners | ✅ Kavali | New banner upload cheyyi |
| DELETE | /api/banners/:id | ✅ Kavali | Banner delete cheyyi |

---

## 🔒 9. Security (Security Ela Pettamu)

1. **OTP Hashing** — bcryptjs tho OTP ni hash chesi DB lo save chestamu
2. **OTP Expiry** — 10 minutes tarvata automatic ga expire avutundi
3. **JWT Middleware** — Anni admin routes lo token signature verify chestamu
4. **CORS Policy** — Only Vercel URL mariyu localhost nundi requests allow chestamu
5. **Protected Routes** — Frontend lo ProtectedRoute.jsx unauthenticated users ni login page ki redirect chestundi
6. **No Password Storage** — Password ledu kabatti password breach risk ledu

---

## 🚀 10. Deployment (Ela Deploy Chesamu)

### Frontend → Vercel
- Framework: Vite (React)
- Build Command: `npm run build`
- Output folder: `dist/`
- GitHub push chesthe auto deploy avutundi

### Backend → Render
- Runtime: Node.js
- Start Command: `node index.js`
- Anni environment variables Render dashboard lo add chesamu
- Free tier — inactivity tarvata cold start avutundi

### Backend Environment Variables
```
MONGODB_URI            → MongoDB Atlas connection string
JWT_SECRET             → JWT sign cheyyadaniki secret key
BREVO_API_KEY          → Brevo email API key
BREVO_USER             → Sender email address
CLOUDINARY_CLOUD_NAME
CLOUDINARY_API_KEY
CLOUDINARY_API_SECRET
```

---

## ⚡ 11. Challenges & Solutions (Eppadi Problems Vachavi, Ela Solve Chesamu)

### Problem 1: Server restart ainapudu images delete avutunnai
**Emi Jarigindi:** Render lo server disk lo save chesthe, restart ainapudu anni images poyevu.
**Ela Solve Chesamu:** multer-storage-cloudinary integrate chesamu. Ippudu images direct ga Cloudinary ki stream avutayi. Server disk use avvadam ledu.

### Problem 2: OTP emails deliver avvadam ledu
**Emi Jarigindi:** Gmail SMTP ports (465/587) Render environment lo block chesaru.
**Ela Solve Chesamu:** Brevo HTTP REST API ki switch chesamu. Idi port 443 (HTTPS) vadutundi — ekkada kuda block avvadam ledu. 100% reliable.

### Problem 3: Admin security weak ga undi
**Emi Jarigindi:** Simple password login brute-force cheyyadaniki easy ga untundi.
**Ela Solve Chesamu:** OTP-based passwordless login implement chesamu. OTPs bcrypt-hashed, time-limited (10 min), mariyu single-use.

### Problem 4: Frontend nundi Backend ki CORS errors vastunayi
**Emi Jarigindi:** Vercel frontend nundi API calls CORS block avutunnai.
**Ela Solve Chesamu:** Express CORS middleware lo explicit allowedOrigins list configure chesamu — only Vercel URL mariyu localhost allow avutayi.

---

## ❓ 12. Interview Questions & Answers

### Q1: Meeru project ni 2-3 lines lo explain cheyandi.
**Jawab:** Sree Neha Events oka full-stack event management web application. Iddulo visitors weddings, birthdays lanti event services ni browse cheyagalaru. Admin matram secure OTP-based JWT authentication tho protected dashboard dwara anni services, pricing, images mariyu banners ni manage cheyagalatadu.

---

### Q2: JWT authentication enduku vadamu?
**Jawab:** JWT (JSON Web Tokens) stateless ga untayi — server database lo session data store cheyyaalsina avasaram ledu. Token secret key tho sign avutundi, kabatti tamper cheyyadam impossible. Admin token localStorage lo store avutundi mariyu anni admin API requests lo Bearer header tho pampistamu.

---

### Q3: bcryptjs ante enti, enduku vadamu?
**Jawab:** bcryptjs oka password hashing library. Memu passwords kaddu OTPs vadutamu, kaani OTP ni kuda database lo save chesukunnappudu hash chestamu. Database ki access vasina kuda real OTP chudadam possible kaadu. Verify chesanapudu bcrypt.compare() tho entered OTP ni stored hash tho compare chestamu.

---

### Q4: Cloudinary integration ela pani chestundi?
**Jawab:** Memu multer-storage-cloudinary ni Multer storage engine ga vadamu. Admin image upload chestinapudu, server disk ki save kaakunda, Multer automatic ga Cloudinary ki file stream chestundi. Cloudinary permanent CDN URL mariyu publicId return chestundi. Rendu ni MongoDB lo save chestamu — URL image display ki, publicId service delete chestinapudu Cloudinary nundi remove cheyyadaniki.

---

### Q5: CORS ante enti, meru ela handle chesaru?
**Jawab:** CORS (Cross-Origin Resource Sharing) ante browser security feature — different domain nundi API calls default ga block avutayi. Mana frontend (Vercel) mariyu backend (Render) different domains lo unnai, kabatti Express CORS middleware lo only Vercel URL mariyu localhost ni allow chesamu. Velere origins automatic ga reject avutayi.

---

### Q6: Vercel mariyu Render madhya teda enti?
**Jawab:** Vercel frontend hosting ki optimize chesindi — static sites mariyu SPAs ni GitHub nundi very efficiently auto-deploy chestundi. Render backend Node.js servers ki vadutamu ekkada persistent server processes run avutayi. Vercel only static files host chestundi; continuous Express server run cheyyalemu.

---

### Q7: Redux kaadu, React Context API enduku vadamu?
**Jawab:** Ee project lo only authentication state (user login info mariyu token) globally share cheyyadam avasaram. Adi kosam React Context API chalu. Redux ante unnecessary complexity mariyu boilerplate code vastundi — adi okke oka chinna global state ki overengineering avutundi.

---

### Q8: Framer Motion ante enti, ela vadamu?
**Jawab:** Framer Motion oka React animation library. Memu danni page transition animations ki (AnimatePresence), buttons mariyu cards meeda hover effects ki, mariyu mobile menu animated dropdown ki vadamu. CSS keyframe animations raayakunda premium, professional UI feel vastundi.

---

### Q9: Image deletion properly ela handle chestunnaru?
**Jawab:** Admin service delete chestinapudu, backend mundu cloudinary.uploader.destroy(publicId) call chestundi — Cloudinary cloud storage nundi image remove avutundi. Tarvata MongoDB lo document delete avutundi. Ee two-step cleanup valla Cloudinary lo orphaned images miguladam ledu.

---

### Q10: Meeru inka time unte emi improve chestaru?
**Jawab:**
1. Services list peddadi ainapudu pagination add chestanu
2. Upload mundhu image optimization (resize/compress) add chestanu
3. Refresh token implement chestanu — admin 24 hours ki okasari login cheyyaalsina avasaram lekunda
4. Analytics dashboard add chestanu — page views mariyu popular services track cheyyadaniki
5. Booking/enquiry form add chestanu — visitors direct ga services request cheyyataniki

---

## 📋 13. Resume Points (Copy Paste Ready)

**Project Name:** Sree Neha Events | Full-Stack Web Application

**Points:**
1. React.js, Node.js, Express.js, mariyu MongoDB vaduthu full-stack event management web app build chesanu — premium event services manage mariyu showcase cheyyadaniki.
2. JWT tokens mariyu bcryptjs hashing tho secure OTP-based authentication implement chesanu; OTPs ni Brevo HTTP API dwara deliver chesamu — firewall-safe email delivery kosam.
3. multer-storage-cloudinary vaduthu Cloudinary cloud storage integrate chesanu — images direct ga cloud ki stream avutayi, server disk dependency zero.
4. Admin ki full CRUD operations tho responsive Admin Dashboard develop chesanu — services, pricing, descriptions mariyu dynamic homepage banner management kosam.
5. Tailwind CSS mariyu Framer Motion tho modern UI design chesanu — animated transitions, category-based filtering, mariyu dynamic auto-sliding banner carousel features tho.

---

*Deployed: Vercel (Frontend) + Render (Backend) | GitHub: github.com/lvs170603*

---

## 🐙 14. Git Commands (Version Control)

### 🔧 Git Setup (Modati Sari Cheyyadam)
```bash
# Meeru peru mariyu email globally set cheyyi
git config --global user.name "Meeru Peru"
git config --global user.email "meeru@email.com"

# Current config chudadam
git config --list
```

---

### 📁 New Project Start Cheyyadam
```bash
# Kొtha git repository initialize cheyyi
git init

# GitHub nundi existing repository clone cheyyi
git clone https://github.com/lvs170603/Sree_neha_events_Frontend.git

# Backend repo clone cheyyi
git clone https://github.com/lvs170603/Sree_neha_events_Backend.git
```

---

### 📝 Roju Vadine Commands (Daily Workflow)
```bash
# Files status check cheyyi (emi maarindi, emi stage aindi)
git status

# Oka specific file ni staging area ki add cheyyi
git add filename.js

# Anni changed files ni staging area ki add cheyyi
git add .

# Message tho commit cheyyi
git commit -m "New service feature add chesanu"

# GitHub ki push cheyyi (main branch)
git push origin main

# GitHub nundi latest changes pull cheyyi
git pull origin main
```

---

### 🌿 Branch Commands
```bash
# Anni branches chudadam
git branch

# Kotta branch create cheyyi
git branch feature/admin-dashboard

# Branch ki switch cheyyi
git checkout feature/admin-dashboard

# Create mariyu switch oka command lo
git checkout -b feature/new-feature

# Branch ni main lo merge cheyyi
git checkout main
git merge feature/admin-dashboard

# Branch delete cheyyi (merge tarvata)
git branch -d feature/admin-dashboard

# Kotta branch ni GitHub ki push cheyyi
git push origin feature/admin-dashboard
```

---

### 🔍 History Chudadam
```bash
# Commit history chudadam
git log

# Short commit history (oka line lo)
git log --oneline

# File lo changes chudadam
git diff filename.js

# Staged changes chudadam
git diff --staged
```

---

### ↩️ Mistakes Correct Cheyyadam (Undo)
```bash
# File ni unstage cheyyi (git add undo)
git restore --staged filename.js

# File lo changes discard cheyyi (edit undo)
git restore filename.js

# Last commit undo (files keep cheyyi)
git reset --soft HEAD~1

# Last commit undo (changes kuda delete) ⚠️ DANGEROUS
git reset --hard HEAD~1

# Specific commit revert cheyyi (safe way)
git revert <commit-hash>
```

---

### 🔗 Remote Repository Commands
```bash
# Connected remote URLs chudadam
git remote -v

# Remote origin add cheyyi
git remote add origin https://github.com/lvs170603/repo-name.git

# Remote URL change cheyyi
git remote set-url origin https://github.com/lvs170603/new-repo.git

# Remote remove cheyyi
git remote remove origin
```

---

### 🏷️ Tags (Versions / Releases Kosam)
```bash
# Tag create cheyyi
git tag v1.0.0

# Tags ni GitHub ki push cheyyi
git push origin --tags

# Anni tags chudadam
git tag
```

---

### 📌 Ee Project lo Vadina Git Commands
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

# Changes chesina tarvata (daily push)
git add .
git commit -m "Cloudinary image upload feature add chesanu"
git push origin main
```

---

### ❓ Git Interview Questions & Answers (Telugu lo)

**Q: git pull mariyu git fetch madhya teda enti?**
A: `git fetch` remote nundi changes download chestundi kaani merge cheyyadu. `git pull` = `git fetch` + `git merge`. Merge mundhu changes preview cheyyadaniki fetch vadatam.

**Q: git merge mariyu git rebase madhya teda enti?**
A: `git merge` rendu branches ni combine chesi new merge commit create chestundi. `git rebase` commits ni inka branch top lo move chestundi — cleaner history kaani commits rewrite avutayi. Team projects lo merge safe ga untundi.

**Q: .gitignore ante enti?**
A: Git ki eelanti files/folders track cheyyakudadani cheppedi file. Example: `node_modules/`, `.env` (secrets untayi), `dist/` (build folder) — ivvi GitHub ki pampakudadu.

**Q: git stash ante enti?**
A: Uncommitted changes ni temporarily save cheyyadam — branch switch chesanapudu commit cheyyakunda. `git stash` tho save cheyyi, `git stash pop` tho restore cheyyi.

**Q: git reset mariyu git revert madhya teda enti?**
A: `git reset` commits ni history nundi remove chestundi (shared repos lo dangerous). `git revert` changes ni undo chese new commit create chestundi — public/shared repos ki safe ga untundi.

---

*Deployed: Vercel (Frontend) + Render (Backend) | GitHub: github.com/lvs170603*
