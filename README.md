# GoGo Rental — Frontend

A car rental booking application built with Next.js 14 and TypeScript. Users can browse available vehicles, select rental dates, complete payment through a mock bank QR flow, and manage their bookings from a personal dashboard. Administrators have a dedicated panel for managing providers, cars, and rental records.

**Live:** [gogorental.vercel.app](https://gogorental.vercel.app)  
**API Docs:** [gogorental.vercel.app/api-docs](https://gogorental.vercel.app/api-docs)  
**Backend:** [github.com/LittleKidz/GoGo_Rental_Application_Backend](https://github.com/LittleKidz/GoGo_Rental_Application_Backend)

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 14 (App Router) |
| Language | TypeScript |
| Styling | Tailwind CSS v4 |
| Auth | NextAuth.js 4 |
| State | Redux Toolkit + Redux Persist |
| API Docs | Swagger UI React |
| Runtime | Node.js 20 |
| Deployment | Vercel |

---

## Getting Started

### Prerequisites

- Node.js 20 or later
- npm
- A running instance of the GoGo Rental backend (see backend README)

### Installation

```bash
git clone https://github.com/LittleKidz/GoGo_Rental_Application.git
cd GoGo_Rental_Application
npm install
```

### Environment Variables

Create a `.env.local` file in the project root:

```env
BACKEND_URL=http://localhost:5000
FRONTEND_URL=http://localhost:3000
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your_nextauth_secret_here
```

| Variable | Description |
|---|---|
| `BACKEND_URL` | Base URL of the Express API (server-side only) |
| `FRONTEND_URL` | Public URL of this app |
| `NEXTAUTH_URL` | Must match the deployed URL for NextAuth callbacks |
| `NEXTAUTH_SECRET` | Secret used to sign session tokens |

### Running Locally

```bash
npm run dev
```

The application will be available at `http://localhost:3000`.

```bash
npm run build   # production build
npm run start   # start production server
```

---

## Project Structure

```
src/
  app/                  # Next.js App Router pages and layouts
  components/           # Shared UI components
    Loading/            # Full-screen and inline loading states
    Toast/              # Toast notifications with useToast() hook
    Modal/              # Reusable modal wrapper
    DateRangePicker/    # Rental date selection component
    DateRangeDisplay/   # Read-only date range display
  libs/
    utils.ts            # Shared utility functions
    proxy.ts            # Server-side API proxy helpers
  store/                # Redux store, slices, and persistence config
public/                 # Static assets
```

---

## Key Features

**Browsing and Booking**  
Users can view all available rental providers and their cars. Each car listing shows brand, model, color, daily rate, and an automatically generated image sourced from the [Imagin Studio CDN](https://cdn.imagin.studio). Selecting a car opens a date range picker that validates availability against existing bookings before creating a rental.

**Payment Flow**  
Payment is handled through a simulated bank QR code. After creating a booking, the user scans a QR code that encodes a URL pointing to a mock bank confirmation page. Once confirmed, the mock bank posts back to the API webhook and the payment page — which polls every three seconds — detects the status change and redirects to the receipt page automatically.

**User Dashboard**  
Authenticated users can view their active and past rentals, check payment status, download receipts, and cancel upcoming bookings if cancellation is allowed (at least three days before the pickup date).

**Provider Reviews**  
After completing a rental, users can leave a rating and comment for the provider. The provider's average rating is recalculated on each review creation or deletion.

**In-App Notifications**  
A notification feed surfaces updates about payments, refund requests, and booking changes. Notifications can be marked read individually or all at once.

**Admin Panel**  
Admin accounts have access to a management panel split into three tabs — Providers, Cars, and Rentals — for full CRUD operations and manual payment status overrides.

---

## Authentication

Authentication is handled through NextAuth.js using a credentials provider that exchanges email and password for a JWT issued by the backend. The token is stored in the NextAuth session and forwarded as a `Bearer` header on all authenticated API requests. Session state is persisted across page refreshes using Redux Persist.

Role-based access is enforced both on the server (API routes) and client (page-level guards). Admin-only pages redirect non-admin users back to the home page.

---

## Docker

A multi-stage Dockerfile is included for containerised deployments.

```bash
docker build \
  --build-arg BACKEND_URL=http://your-backend:5000 \
  --build-arg NEXTAUTH_URL=http://your-domain \
  --build-arg NEXTAUTH_SECRET=your_secret \
  -t gogo-rental-frontend .

docker run -p 3000:3000 gogo-rental-frontend
```

The image uses a three-stage build (dependency install, Next.js build, minimal production runtime) on `node:20-alpine`. The final image runs as a non-root user.

---

## API Proxying

All browser-originated API calls are routed through Next.js API route handlers located in `src/app/api/`. These handlers read `process.env.BACKEND_URL` at runtime and forward the request server-side, avoiding CORS issues and keeping the backend URL out of the client bundle. The shared `libs/proxy.ts` utility handles request forwarding and error normalisation consistently across all proxy routes.

---

## Deployment

The application is deployed on Vercel. Set the following environment variables in the Vercel project settings:

```
BACKEND_URL        → your deployed backend URL (not exposed to browser)
FRONTEND_URL       → https://your-vercel-domain.vercel.app
NEXTAUTH_URL       → https://your-vercel-domain.vercel.app
NEXTAUTH_SECRET    → a secure random string
```

Vercel handles the Next.js build automatically on each push to `main`.
git