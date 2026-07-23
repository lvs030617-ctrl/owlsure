# AMC Planner - Complete Project Documentation & Technical Overview

Eee document lo mana **AMC Planner** project ki sambandhinchina complete setup, folder structure, backend APIs, frontend UI flow, mariyu Clean Architecture gurinchi motham details Roman Telugu lo vivarinchadainiki tayaru cheyabadaindi.

---

## 1. Project Overview (Project Gurinchi)

**AMC Planner** anedhi industrial maintenance, annual maintenance contracts (AMC), mariyu machine preventive maintenance (PM) details ni view, search, mariyu monitor cheyadam kosam tayaru chesina cross-platform application.

* **Frontend Framework**: Flutter (Stable, Material Design 3, Provider State Management, Clean Architecture, Repository Pattern).
* **Backend Framework**: Node.js Express Server (Port 5000).
* **Database / Data Source**: Google Sheets API v4 (Service Account Authentication tho connect cheyabadaindi).
* **Supported Platforms**: Android, Windows Desktop, Linux, Web.

---

## 2. Overall Project Flow (System Flow How It Works)

```
+-----------------------------------------------------------------------+
|                         Google Sheets API                             |
|       (Contains Area list, Planner list, complete AMC details)       |
+-----------------------------------------------------------------------+
                                   ▲
                                   │ (Service Account Credentials JSON)
                                   ▼
+-----------------------------------------------------------------------+
|                    Node.js Express Backend API                        |
|                        (http://localhost:5000)                        |
|                                                                       |
|  - GET /api/areas           --> Extract unique areas                  |
|  - GET /api/planners?area=  --> Filter planners by selected area      |
|  - GET /api/planner/:code   --> Fetch complete 15-field detail item   |
+-----------------------------------------------------------------------+
                                   ▲
                                   │ (HTTP REST Requests / JSON)
                                   ▼
+-----------------------------------------------------------------------+
|                      Flutter App (AMC Planner)                        |
|                        (http://localhost:8080)                        |
|                                                                       |
|  1. Splash Screen (2s Fade & Scale Animation)                         |
|  2. Home Screen (Dropdown 1: Area Selection --> Dropdown 2: Code)     |
|  3. Details Screen (Material 3 Cards displaying PO, Dates, PM Stats)   |
|  4. Search Screen (Instant live filtering by Planner, Customer, PO)   |
+-----------------------------------------------------------------------+
```

---

## 3. Project Directory Structure (Folder Structure)

### 🔹 Node.js Express Backend (`backend/`)
```
backend/
├── config/
│   ├── envConfig.js              # Environment variables loader (.env)
│   ├── googleSheetsConfig.js     # Google Sheets Service Account authentication
│   └── service-account.json      # Service account secret key file
├── controllers/
│   ├── areasController.js        # GET /api/areas controller logic
│   ├── plannersController.js     # GET /api/planners controller logic
│   └── plannerDetailController.js# GET /api/planner/:plannerNo controller logic
├── services/
│   ├── googleSheetsService.js    # Raw sheet reading & 2D array parsing
│   ├── areasService.js           # Unique areas extractor
│   ├── plannersService.js        # Filter planner numbers by area
│   └── plannerDetailService.js   # Detailed item extractor matching Planner No
├── routes/
│   ├── apiRoutes.js              # Master API Router
│   ├── areasRoutes.js            # /api/areas router
│   ├── plannersRoutes.js         # /api/planners router
│   └── plannerDetailRoutes.js    # /api/planner router
├── .env                          # Config (PORT=5000, GOOGLE_SHEET_ID, etc.)
├── package.json                  # Dependencies (express, cors, googleapis, dotenv)
└── server.js                     # Main Express server entry point
```

### 🔹 Flutter App (`lib/`)
```
lib/
├── main.dart                     # App entry point with MultiProvider setup
├── config/
├── app_config.dart               # Base URL configuration (Platform Aware)
├── app_theme.dart                # Material 3 Light & Dark themes
├── colors.dart                   # Red primary accent & white background colors
└── routes.dart                   # Named route definitions (/splash, /home, /search, /details)
├── models/
│   ├── amc_item.dart             # Full AMC Planner detail data model
│   ├── area_model.dart           # Area data model (id, area)
│   └── planner_no_model.dart     # Planner number model (plannerNo)
├── services/
│   ├── api_service.dart          # HTTP client, status code handling, exceptions
│   └── storage_service.dart      # SharedPreferences local storage helper
├── repositories/
│   ├── amc_repository.dart       # Abstract repository interface (Clean Architecture)
│   └── amc_repository_impl.dart  # Concrete repository implementation
├── providers/
│   ├── amc_provider.dart         # Main state manager (Dropdowns, Search, Pull to Refresh)
│   ├── app_provider.dart         # Root app level provider
│   └── theme_provider.dart       # Theme mode state manager
├── widgets/
│   └── error_state_widget.dart   # Universal Error, Offline, Timeout & Retry UI
└── screens/
    ├── splash_screen.dart        # Animated splash screen (2 Seconds)
    ├── home_screen.dart          # Main selection screen with dynamic cascading dropdowns
    ├── search_screen.dart        # Real-time multi-field search screen
    └── details_screen.dart       # Comprehensive Material 3 metadata details card view
```

---

## 4. Backend APIs & Responses (API Implementation)

### 📌 1. Unique Areas List API
* **Endpoint**: `GET /api/areas`
* **Description**: Google Sheet lo unna unique Area names nu array of objects ga return chestundhi.
* **Sample Response**:
```json
[
  { "id": 1, "area": "QC" },
  { "id": 2, "area": "P1" },
  { "id": 3, "area": "P2" },
  { "id": 4, "area": "P3" }
]
```

---

### 📌 2. Planners List By Area API
* **Endpoint**: `GET /api/planners?area=P1`
* **Query Parameter**: `area` (e.g. `P1`, `QC`)
* **Description**: User select chesina Area ki sambandhinchina Planner Numbers (Codes) nu filter chesi return chestundhi.
* **Sample Response**:
```json
[
  { "plannerNo": "P1/WEB/001" },
  { "plannerNo": "P1/WEB/002" },
  { "plannerNo": "P1/WEB/003" }
]
```

---

### 📌 3. Complete Planner Details API
* **Endpoint**: `GET /api/planner/:plannerNo` (or `GET /api/planner?plannerNo=P1/WEB/002`)
* **Description**: Specific Planner Number ki sambandhinchina complete 15 metadata fields nu JSON response ga return chestundhi.
* **Sample Response**:
```json
{
  "plannerNumber": "P1/WEB/002",
  "poNumber": "98768678",
  "customerName": "Acme Printing & Industrial Corp",
  "machineName": "Web Offset Printing Machine #2",
  "location": "Plant 1 / Area P1",
  "startDate": "20-02-2026",
  "endDate": "22-09-2026",
  "plannedPm": "02",
  "completedPm": "02",
  "pendingPm": "00",
  "firstPmDate": "22-03-2026",
  "secondPmDate": "22-09-2026",
  "thirdPmDate": "N/A",
  "fourthPmDate": "N/A",
  "remarks": "All planned preventive maintenance visits executed successfully."
}
```

---

## 5. Flutter Frontend Features & Flow (App Key Features)

1. **Splash Screen (`splash_screen.dart`)**:
   - White background meedha Bold Red `AMC PLANNER` title unthundhi.
   - 2-second combined `FadeTransition` mariyu `ScaleTransition` animation tho auto-navigate ayi Home Screen ki veltundhi.

2. **Cascading Dynamic Dropdowns (`home_screen.dart`)**:
   - **Dropdown 1 (Select Area)**: App load avagane `GET /api/areas` endpoint dwara dynamic ga Backend nunchee Area list fetch chestundhi. Hardcoded values use cheyadaledhu.
   - **Dropdown 2 (Select Planner Code)**: Area select chesina ventane, auto ga `GET /api/planners?area={SelectedArea}` query request send chesi, second dropdown in real-time populate chestundhi. Loading state mariyu empty state displays kooda unnayi.

3. **Details View (`details_screen.dart`)**:
   - Planner Code select chesthe auto ga `GET /api/planner/{plannerNo}` fetch chesi Details Screen open avuthundhi.
   - Material 3 Cards & Modern UI lo motham 15 details display avuthayi:
     - Planner Number Badge (Header)
     - General Info (PO Number, Customer Name, Machine Name, Location)
     - Contract Dates (Start Date, End Date)
     - PM Metrics Tiles (Planned PM, Completed PM, Pending PM)
     - Execution Dates (First PM, Second PM, Third PM, Fourth PM Dates)
     - Remarks & Notes

4. **Instant Multi-Field Search (`search_screen.dart`)**:
   - Home Screen AppBar search icon / shortcut card tap chesthe Search View open avuthundhi.
   - Live query typing dwara Planner Number, Customer Name, mariyu PO Number all three fields meedha instant real-time filtering panichestundhi.

5. **Pull-To-Refresh**:
   - Both Home Screen mariyu Details Screen lo `RefreshIndicator` (Pull-Down to Refresh) mariyu AppBar Refresh Icon button valla real-time data reloading backend nunchee auto-fetch avuthundhi.

6. **Comprehensive Error Handling (`error_state_widget.dart`)**:
   - Internet lekapothe (**No Internet Connection**), server offline unte (**Server Error 500**), request late ayithe (**Timeout**), ledha data lekapothe (**No Data Found**), user-friendly modern Error UI tho pata **Try Again / Retry Button** provide cheyabadaindi.

---

## 6. Project How to Run Instructions (Ela Run Cheyali)

### Step 1: Backend Server Start Cheyatam
Terminal lo backend folder ki velli, dependencies start cheyali:
```bash
cd backend
npm install
npm run dev
```
Backend API server `http://localhost:5000` lo starting output vasthundhi.

### Step 2: Flutter App Run Cheyatam
Main project directory lo Flutter app run cheyali:

* **For Web**:
```bash
flutter run -d web-server --web-port 8080
```
  App Browser URL: `http://localhost:8080`

* **For Linux Desktop**:
```bash
flutter run -d linux
```

* **For Android Emulator / Physical Device**:
```bash
flutter run -d android
```

---

## 7. Quality & Clean Architecture Verification

* **Flutter Code Analysis**: `flutter analyze` command run cheyabadindhi -> **No issues found (0 warnings, 0 errors)**.
* **Code Standards**: Material 3 guidelines, Provider state management, Repository abstraction pattern, 100% Null Safety, mariyu production-ready clean code structure follow avuthundhi.
