# DocNode — Healthcare Scheduling & Clinical Management Platform

DocNode is a full-stack healthcare platform engineered to streamline clinical appointments, doctor schedules, patient profiles, and medical practice workflows. The system features decoupled, responsive web interfaces for patients and healthcare administrators, backed by a RESTful Node.js service, MongoDB persistence, Cloudinary asset storage, and Razorpay payment processing.

---

## Architecture Overview

DocNode is organized as a modular monorepo containing three core components:

* **Patient Portal (`frontend`)**: React application for patients to explore medical specialists, schedule visits, manage appointments, leave reviews, and complete payments.
* **Doctor & Admin Console (`admin`)**: Dedicated portal for clinic management, doctor onboarding, availability scheduling, and appointment lifecycle tracking.
* **API Service (`backend`)**: Express.js REST API handling role-based authentication, appointment concurrency, transactional payment verification, and cloud asset pipelines.

```
DocNode/
├── frontend/          # Patient web portal (React 19 + Vite + Tailwind CSS)
├── admin/             # Healthcare admin & doctor portal (React 19 + Vite + Tailwind CSS)
└── backend/           # Core API service (Node.js + Express + MongoDB)
```

---

## Key Features

### Patient Portal
* **Specialist Discovery**: Filter doctors by clinical specialty (General Physician, Gynecologist, Dermatologist, Pediatrician, Neurologist, Gastroenterologist) with real-time availability badges.
* **Intelligent Slot Scheduling**: Select available consultation dates and time slots, eliminating scheduling overlaps.
* **Appointment Management**: View active and historical appointments, reschedule upcoming sessions, or cancel bookings.
* **Integrated Payments**: Secure checkout with Razorpay for instant consultation fee settlement and automated status verification.
* **Doctor Ratings & Reviews**: Post patient feedback and average ratings for medical professionals.
* **Profile Management**: Maintain personal health profile, contact information, and avatar uploads via Cloudinary.

### Administration & Doctor Portal
* **Executive Dashboard**: Real-time operational metrics tracking total doctors, scheduled appointments, patient volume, and revenue.
* **Doctor Management**: Onboard new physicians with specialization details, degree credentials, consultation fee structures, and profile photography.
* **Schedule & Availability Controls**: Toggle doctor availability statuses instantly to prevent appointments during leaves or off-duty hours.
* **Appointment Tracking**: Complete audit log of all bookings across doctors with status indicators (Completed, Cancelled, Pending).

### Backend Engineering
* **Role-Based Access Control (RBAC)**: Distinct JWT middleware pipelines for patients (`authUser`) and administrative personnel (`authAdmin`).
* **Payment Webhook & Verification**: Cryptographic signature validation for Razorpay orders to prevent order tampering.
* **Automated Asset Pipelines**: Image processing and cloud storage through Multer and Cloudinary.
* **Password Recovery**: Secure tokenized password reset flows powered by Nodemailer.

---

## Tech Stack

### Frontend & Admin
* **Library / Runtime**: React 19, Vite
* **Styling**: Tailwind CSS
* **Routing**: React Router DOM (v7)
* **State & Networking**: React Context API, Axios
* **Notifications**: React-Toastify

### Backend & Database
* **Runtime / Framework**: Node.js (ES Modules), Express.js (v5)
* **Database**: MongoDB with Mongoose ODM
* **Authentication**: JSON Web Tokens (JWT), Bcrypt
* **Payment Processing**: Razorpay SDK
* **File Storage**: Multer, Cloudinary SDK
* **Email Service**: Nodemailer

---

## Getting Started

### Prerequisites
* [Node.js](https://nodejs.org/) (v18.0.0 or higher recommended)
* [MongoDB](https://www.mongodb.com/) (Local instance or MongoDB Atlas cluster URI)
* [Cloudinary](https://cloudinary.com/) account for image storage
* [Razorpay](https://razorpay.com/) test/live API credentials

---

### Installation & Environment Setup

#### 1. Clone the repository
```bash
git clone https://github.com/ajaykumar-8606/DocNode.git
cd DocNode
```

#### 2. Configure Backend
Navigate to the `backend` directory and install dependencies:
```bash
cd backend
npm install
```

Create a `.env` file in `backend/` with the following variables:
```env
PORT=9000
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret_key

# Admin Credentials
ADMIN_EMAIL=admin@docnode.com
ADMIN_PASSWORD=your_secure_admin_password

# Cloudinary Storage
CLOUDINARY_NAME=your_cloudinary_cloud_name
CLOUDINARY_API_KEY=your_cloudinary_api_key
CLOUDINARY_SECRET_KEY=your_cloudinary_api_secret

# Razorpay Payment Gateway
RAZORPAY_KEY_ID=your_razorpay_key_id
RAZORPAY_KEY_SECRET=your_razorpay_key_secret

# Nodemailer (Optional for password recovery)
SMTP_USER=your_smtp_email
SMTP_PASS=your_smtp_app_password
```

Start the backend development server:
```bash
npm run dev
# Server runs on http://localhost:9000
```

---

#### 3. Configure Patient Frontend
In a new terminal window, navigate to `frontend/`:
```bash
cd ../frontend
npm install
```

Create a `.env` file in `frontend/`:
```env
VITE_BACKEND_URL=http://localhost:9000
VITE_RAZORPAY_KEY_ID=your_razorpay_key_id
```

Start the patient portal:
```bash
npm start
# Application runs on http://localhost:5173
```

---

#### 4. Configure Admin Console
In a new terminal window, navigate to `admin/`:
```bash
cd ../admin
npm install
```

Create a `.env` file in `admin/`:
```env
VITE_BACKEND_URL=http://localhost:9000
```

Start the admin console:
```bash
npm start
# Application runs on http://localhost:5174
```

---

## API Endpoints Reference

### Public & Doctor Routes (`/api/doctor`)
* `GET /api/doctor/list` — Retrieve list of verified doctors and availability

### User Routes (`/api/user`)
* `POST /api/user/register` — Patient registration
* `POST /api/user/login` — Patient authentication
* `POST /api/user/reset-password` — Password reset trigger
* `GET /api/user/get-profile` — Fetch authenticated patient profile
* `POST /api/user/update-profile` — Update patient records and avatar
* `POST /api/user/book-appointment` — Reserve consultation slot
* `GET /api/user/list-appointments` — Retrieve patient appointment history
* `POST /api/user/cancel-appointment` — Cancel booked consultation
* `POST /api/user/reschedule-appointment` — Change consultation time slot
* `POST /api/user/appointment-payment` — Generate Razorpay payment order
* `POST /api/user/verify-razorpay` — Verify cryptographic payment signature
* `POST /api/user/add-review` — Submit rating and review for a doctor
* `GET /api/user/doctor-reviews/:docId` — Retrieve reviews for a doctor

### Admin Routes (`/api/admin`)
* `POST /api/admin/login` — Administrator authentication
* `POST /api/admin/add-doctor` — Onboard new physician (Multer image upload)
* `POST /api/admin/all-doctors` — List all registered doctors
* `POST /api/admin/change-availability` — Toggle doctor booking status
* `GET /api/admin/appointments` — Fetch platform-wide appointments
* `POST /api/admin/cancel-appointment` — Administrative appointment cancellation
* `GET /api/admin/dashboard` — Platform performance and revenue statistics

---

## Engineering Design & Concurrency Handling

* **Conflict-Free Slot Allocation**: Time slot booking enforces validation checks against concurrent reservations to eliminate double-booking hazards.
* **Cryptographic Verification**: Payment transactions enforce server-side HMAC SHA-256 signature verification matching Razorpay order payloads before updating appointment payment states to `true`.
* **Zero Direct Database Blobs**: Medical certificates and doctor images are directly offloaded to CDN storage (Cloudinary), keeping MongoDB document footprints lean and read queries optimized.

---

## License

This project is licensed under the [ISC License](LICENSE).
