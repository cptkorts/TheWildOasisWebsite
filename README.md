# The Wild Oasis — Hotel Booking Website

A customer-facing booking website for **The Wild Oasis**, a fictional luxury cabin hotel. Guests can browse available cabins, check real-time availability, sign in with Google, book a stay, and manage their reservations and profile — all backed by a shared Supabase database.

This website is the **guest-facing companion app** to [The Wild Oasis management dashboard](https://github.com/cptkorts), the internal tool hotel staff use to manage cabins, pricing, discounts, bookings and guests. Both applications read from and write to the **same Supabase (PostgreSQL) database**, so a cabin, price or discount created/updated by staff in the management dashboard is immediately reflected here, and a booking placed by a guest on this site immediately shows up in the dashboard.

## Features

- 🏕️ **Browse cabins** — list of all cabins with images, capacity and pricing (incl. discounted price when applicable)
- 🔎 **Filter by capacity** — filter cabins by number of guests (small / medium / large)
- 📅 **Live availability calendar** — date-range picker that disables already-booked and past dates, pulled straight from the shared `bookings` table
- 💰 **Dynamic price calculation** — computes total price based on selected nights, regular price and any active discount
- 🔐 **Authentication** — Google sign-in via NextAuth; a `guests` record is created automatically on first login
- 🧾 **Reservations** — create a booking, view all personal reservations, edit guest count/observations, or cancel a reservation
- 👤 **Guest profile** — update nationality (with country flag, fetched from the REST Countries API) and national ID
- 🛡️ **Authorization checks** — server actions verify the signed-in guest actually owns a booking before letting them edit/delete it
- ⚙️ **Shared hotel settings** — booking rules (min/max nights, breakfast price, etc.) are read from a `settings` table managed by the hotel staff dashboard
- ⚡ **Server Components & Server Actions** — data fetching and mutations happen on the server (Next.js App Router), with optimistic UI feedback (spinners, pending states)

## Tech Stack

| Category            | Technology                                                                 |
| -------------------- | --------------------------------------------------------------------------- |
| Framework            | [Next.js 14](https://nextjs.org/) (App Router, Server Components, Server Actions) |
| UI Library           | [React 18](https://react.dev/)                                             |
| Styling              | [Tailwind CSS](https://tailwindcss.com/)                                   |
| Database / Backend   | [Supabase](https://supabase.com/) (PostgreSQL) — **shared with the hotel management app** |
| Authentication       | [NextAuth.js (Auth.js v5)](https://authjs.dev/) with Google OAuth provider  |
| Date utilities       | [date-fns](https://date-fns.org/)                                          |
| Date picker          | [react-day-picker](https://daypicker.dev/)                                 |
| Icons                | [Heroicons](https://heroicons.com/)                                        |
| Linting / Formatting | ESLint (`eslint-config-next`), Prettier + `prettier-plugin-tailwindcss`     |

## Architecture notes

- The Supabase project contains at least the following tables, shared between this website and the management dashboard: `cabins`, `bookings`, `guests`, `settings`.
- This app only creates/updates `guests` (on sign-in / profile update) and `bookings` (create/update/delete, scoped to the signed-in guest). Cabins, prices, discounts and global settings are owned/managed by the staff dashboard and are only **read** here.
- Supabase credentials are only ever used in server-side code (`app/_lib/supabase.js`, consumed by Server Components and Server Actions), so they are never bundled into the client.

## Getting Started

### Prerequisites

- Node.js 18+ and npm
- A Supabase project with the shared schema described above
- A Google Cloud OAuth 2.0 Client (for sign-in)

### 1. Clone and install

```bash
git clone https://github.com/cptkorts/TheWildOasisWebsite.git
cd TheWildOasisWebsite
npm install
```

### 2. Configure environment variables

Create a `.env.local` file in the project root (this file is git-ignored and must **never** be committed):

```bash
SUPABASE_URL=your-supabase-project-url
SUPABASE_KEY=your-supabase-api-key

NEXTAUTH_URL=http://localhost:3000/
NEXTAUTH_SECRET=generate-a-random-secret

AUTH_GOOGLE_ID=your-google-oauth-client-id
AUTH_GOOGLE_SECRET=your-google-oauth-client-secret
```

> Generate `NEXTAUTH_SECRET` with `openssl rand -base64 32` (or any secure random string generator).

### 3. Run the development server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) to view the app.

### Other scripts

```bash
npm run build   # production build
npm run start   # start the production server
npm run prod    # build + start
npm run lint    # run ESLint
```

## Project structure

```
app/
  _components/   # Reusable UI components (Cabin list, Reservation form, Nav, etc.)
  _lib/          # Data layer: supabase client, data-service (queries), actions (server actions), auth config
  about/         # About page
  account/       # Protected guest area: profile & reservations (requires sign-in)
  api/           # NextAuth route handlers
  cabins/        # Cabin listing & detail/booking pages
  login/         # Sign-in page
middleware.js    # Protects /account routes via NextAuth
```

## License

No license has been specified for this project yet. Consider adding a `LICENSE` file (e.g. MIT) before accepting external contributions.
