# Next.js Firebase Starter

A production-ready starter template for building full-stack web applications with **Next.js 16**, **React 19**, and **Firebase**. This template ships with email/password authentication, Firestore data helpers, a route-protected admin page, and a global auth context so you can skip the boilerplate and start building.

---

## Tech Stack

| Layer          | Technology                                                       |
| -------------- | ---------------------------------------------------------------- |
| Framework      | [Next.js 16](https://nextjs.org/) (App Router)                   |
| UI Library     | [React 19](https://react.dev/)                                   |
| Styling        | [Tailwind CSS](https://tailwindcss.com/)                         |
| Language       | [TypeScript](https://www.typescriptlang.org/)                    |
| Backend / Auth | [Firebase](https://firebase.google.com/) (Auth + Firestore)      |
| Linting        | [ESLint](https://eslint.org/) (flat config)                      |

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

- **Node.js 20.19+, 22.13+, or 24+** (the project's effective floor per `package-lock.json`; Next.js 16 itself only requires `>=20.9.0`, but `eslint-visitor-keys@5` pulled in by `@typescript-eslint` needs the narrower range above)
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

Copy the example file and fill in your Firebase project's values:

```bash
cp .env.local.example .env.local
```

```env
NEXT_PUBLIC_FIREBASE_API_KEY=your_api_key
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=your_auth_domain
NEXT_PUBLIC_FIREBASE_PROJECT_ID=your_project_id
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=your_storage_bucket
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=your_messaging_sender_id
NEXT_PUBLIC_FIREBASE_APP_ID=your_app_id
```

See [Set Up Firebase](#set-up-firebase) below for where to find these values.

> **Note:** these are client-side config values, not secrets. Firebase ships them to every visitor's browser by design; real protection for your data comes from [Firestore Security Rules](https://firebase.google.com/docs/firestore/security/get-started) and API key restrictions configured in the Firebase Console, not from hiding this config. `.env.local` is still gitignored as a convenience so you don't have to retype these locally, but there's no need to treat the values themselves as sensitive.

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
6. _(Optional)_ Go to **Build > Firestore Database** and create a database to use the Firestore helpers.

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

| Function                        | Description                                                                            |
| ------------------------------- | -------------------------------------------------------------------------------------- |
| `addData(collection, id, data)` | Writes (or merges) a document at `collection/id` using `setDoc` with `{ merge: true }` |
| `getData(collection, id)`       | Reads a single document from `collection/id` using `getDoc`                            |

Both return `{ result, error }` for consistent error handling.

---

## Continuous Integration

This repo's GitHub Actions workflow inventory:

| Workflow                | What it does                                                                | Runs on |
| ------------------------ | ---------------------------------------------------------------------------- | ------- |
| `ci.yml`                  | Runs `npm run lint` and `npm run build` (which also type-checks)             | every pull request |
| `dependency-review.yml`   | Scans dependency manifest changes against GitHub's advisory database        | every pull request |
| `label.yml`                | Auto-labels PRs                                                              | every pull request |
| `merge-gatekeeper.yml`     | Aggregates every other check into a single required status                   | every pull request |
| `automerge.yml`            | Approves and merges Dependabot PRs (patch/minor for any ecosystem, or any semver level for `github_actions` updates) | Dependabot PRs only |
| `dependabot-rebase-cascade.yml` | Rebases other open Dependabot PRs after one merges                      | when a Dependabot PR closes |
| `release.yml`               | Runs `standard-version` to bump the version and write the changelog locally | pushes to `main` touching `src/`, `package.json`, or `package-lock.json` |

> **Note:** `release.yml` commits and tags locally via `standard-version` but does not push them anywhere, the runner's working copy is discarded when the job ends. If you want the version bump and changelog to actually persist, add a push step (or `git push --follow-tags`) to the workflow.

If you fork this template and want `ci.yml` to pass, you need to configure your Firebase config as **GitHub Actions repository Variables** (not Secrets):

1. Go to your repo's **Settings > Secrets and variables > Actions > Variables tab**.
2. Add the same six `NEXT_PUBLIC_FIREBASE_*` values from your `.env.local` as repository Variables.

**Why Variables and not Secrets:** the `/admin` route calls Firebase's `getAuth()` at build time, so `npm run build` needs real-looking config values even in CI. `ci.yml` triggers on plain `pull_request`, and GitHub Actions Secrets are unavailable to `pull_request`-triggered workflow runs that Dependabot opened, so storing this config as a Secret would silently break the build check on every Dependabot PR, the exact case `ci.yml` exists to protect. Variables don't have this restriction, and are the right fit anyway since this config is already public by Firebase's own `NEXT_PUBLIC_` convention.

If you want Dependabot to auto-merge PRs and rebase stale ones, also add a `DEPENDABOT_REBASE_PAT` repository **secret** under **Settings > Secrets and variables > Actions** (not the separate Dependabot secrets store, `automerge.yml` and `dependabot-rebase-cascade.yml` both use `pull_request_target`, which runs with the base repository's full permissions and secret access regardless of who opened the PR, unlike plain `pull_request`): a fine-grained personal access token (no expiration, scoped to this repo, with `Contents: Read and write` + `Pull requests: Read and write`). The default `GITHUB_TOKEN` can't trigger other workflow runs (GitHub's anti-recursion protection), so a PAT is required for the rebase-cascade workflow to actually fire after a merge.

## Available Scripts

| Command           | Description                                                      |
| ----------------- | ---------------------------------------------------------------- |
| `npm run dev`     | Start the development server at `http://localhost:3000`          |
| `npm run build`   | Build the application for production                             |
| `npm run start`   | Start the production server (requires `build` first)             |
| `npm run lint`    | Run ESLint across the project                                    |
| `npm run release` | Bump the version and generate a changelog via `standard-version` |

---

## Deployment

### Vercel (recommended)

1. Push your repository to GitHub.
2. Import the project at [vercel.com/new](https://vercel.com/new).
3. Add all `NEXT_PUBLIC_FIREBASE_*` environment variables in the Vercel project settings.
4. Deploy. Vercel handles builds and previews automatically on every push.

This is separate from the GitHub Actions setup above: Vercel environment variables only affect Vercel's own builds, not the `ci.yml` workflow that runs on GitHub. You need both configured for a PR to show a green build check on GitHub *and* a working Vercel preview.

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
