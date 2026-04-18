# Next.js Firebase Starter

A production-ready starter template for building full-stack web applications with **Next.js 15**, **React 19**, and **Firebase**. This template ships with email/password authentication, Firestore data helpers, a route-protected admin page, and a global auth context so you can skip the boilerplate and start building.

---

## Tech Stack

|Layer|Technology|
|-----|---------|
|Framework|[Next.js 15](https://nextjs.org/) (App Router)|
|UI Library|[React 19](https://react.dev/)|
|Styling|[Tailwind CSS](https://tailwindcss.com/)|
|Language|[TypeScript](https://www.typescriptlang.org/)|
|Backend / Auth|[Firebase](https://firebase.google.com/) (Auth + Firestore)|
|Linting|[ESLint](https://eslint.org/) + [Prettier](https://prettier.io/)|

---

## Features

- **Email / Password Authentication** - sign-up, sign-in, and sign-out flows backed by Firebase Auth
- **Global Auth Context** - a React context provider (`AuthContextProvider`) wraps the app and exposes the current user via `useAuthContext()`
- **Protected Routes** - the `/admin` route redirects unauthenticated users to the home page
- **Firestore Helpers** - typed `addData` and `getData` utilities for reading and writing documents
- **App Router** - built on the Next.js App Router with server components, layouts, and file-based routing
- **Inter Font** - automatically loaded and optimized via `next/font/google`

---

## Prerequisites

- **Node.js 18** or higher
- **npm** (bundled with Node.js)
- A [Firebase project](https://console.firebase.google.com/)

---

## Getting Started

### 1. Use the template

Click **Use this template** on GitHub to create your own repository, then clone it:

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
npm install
```

### 2. Configure environment variables

Create a `.env.local` file in the project root:

```bash
cp .env.local.example .env.local   # if the example file exists, otherwise create it
```

Add your Firebase project credentials (see [Set Up Firebase](#set-up-firebase) below):

```env
NEXT_PUBLIC_FIREBASE_API_KEY=your_api_key
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=your_auth_domain
NEXT_PUBLIC_FIREBASE_PROJECT_ID=your_project_id
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=your_storage_bucket
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=your_messaging_sender_id
NEXT_PUBLIC_FIREBASE_APP_ID=your_app_id
```

> **Important:** `.env.local` is already listed in `.gitignore`. Never commit real credentials to version control.

### 3. Run the development server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser. The page hot-reloads as you edit files under `src/`.

---

## Set Up Firebase

1. Go to [https://console.firebase.google.com/](https://console.firebase.google.com/) and sign in with your Google account.
2. Click **Add project**, give it a name, and click **Create project**.
3. On the project overview page, click the **web** icon (`</>`) to register a web app.
4. Give the app a nickname and click **Register app**. Firebase will display your config object. Copy the values into `.env.local`.
5. In the left sidebar, go to **Build > Authentication > Sign-in method** and enable **Email/Password**.
6. *(Optional)* Go to **Build > Firestore Database** and create a database to use the Firestore helpers.

---

## Project Structure

```text
src/
├── app/                        # Next.js App Router pages and layouts
│   ├── admin/
│   │   └── page.tsx            # Protected page, redirects unauthenticated users
│   ├── signin/
│   │   └── page.tsx            # Sign-in page
│   ├── signup/
│   │   └── page.tsx            # Sign-up page
│   ├── globals.css             # Global styles (Tailwind base imports)
│   ├── layout.tsx              # Root layout, wraps app in AuthContextProvider
│   └── page.tsx                # Home page
│
├── context/
│   └── AuthContext.tsx         # Auth context provider and useAuthContext hook
│
└── firebase/
    ├── config.ts               # Firebase app initialization (singleton)
    ├── auth/
    │   ├── signIn.ts           # signInWithEmailAndPassword wrapper
    │   └── signup.ts           # createUserWithEmailAndPassword wrapper
    └── firestore/
        ├── addData.ts          # setDoc helper with merge support
        └── getData.js          # getDoc helper
```

---

## Authentication

Authentication is handled through Firebase Auth and surfaced app-wide via a React context.

**`src/context/AuthContext.tsx`** subscribes to `onAuthStateChanged` and exposes `{ user }` to any component that calls `useAuthContext()`. The root layout wraps the entire app in this provider, so auth state is always available client-side.

**`src/firebase/auth/`** contains two thin async wrappers:

- `signIn(email, password)` - calls `signInWithEmailAndPassword` and returns `{ result, error }`
- `signup(email, password)` - calls `createUserWithEmailAndPassword` and returns `{ result, error }`

Both functions return a consistent `{ result, error }` shape so callers can handle errors without try/catch.

**Protected routes** - `src/app/admin/page.tsx` demonstrates the pattern: read `user` from `useAuthContext()` inside a `useEffect`, then call `router.push("/")` if the user is `null`.

---

## Firestore Helpers

Two utility functions live in `src/firebase/firestore/`:

|Function|Description|
|-------|---------|
|`addData(collection, id, data)`|Writes (or merges) a document at `collection/id` using `setDoc` with `{ merge: true }`|
|`getData(collection, id)`|Reads a single document from `collection/id` using `getDoc`|

Both return `{ result, error }` for consistent error handling.

---

## Available Scripts

|Command|Description|
|-------|---------|
|`npm run dev`|Start the development server at `http://localhost:3000`|
|`npm run build`|Build the application for production|
|`npm run start`|Start the production server (requires `build` first)|
|`npm run lint`|Run ESLint across the project|
|`npm run release`|Bump the version and generate a changelog via `standard-version`|

---

## Deployment

### Vercel (recommended)

1. Push your repository to GitHub.
2. Import the project at [vercel.com/new](https://vercel.com/new).
3. Add all `NEXT_PUBLIC_FIREBASE_*` environment variables in the Vercel project settings.
4. Deploy. Vercel handles builds and previews automatically on every push.

### Other platforms

Set the same `NEXT_PUBLIC_FIREBASE_*` environment variables in your host's dashboard and run:

```bash
npm run build
npm run start
```

See the [Next.js deployment documentation](https://nextjs.org/docs/deployment) for platform-specific guidance.

---

## Contributing

Contributions are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request. Bug reports and feature requests can be filed via [GitHub Issues](../../issues).

## Security

Please review [SECURITY.md](SECURITY.md) for the project's vulnerability disclosure policy.

## License

This project is licensed under the **MIT License**. See [LICENSE](LICENSE) for details.

---

## Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [Firebase Documentation](https://firebase.google.com/docs)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
