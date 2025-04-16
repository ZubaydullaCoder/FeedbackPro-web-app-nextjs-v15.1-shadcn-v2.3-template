# FeedbackPro Development Breakdown

---

**Web Application Development Breakdown: FeedbackPro V1.0 (Full-Stack Next.js Option - Optimized Structure)**

**(As of: Tuesday, April 15, 2025)**

**Project Goal:** To build a web application using a unified Next.js codebase, allowing businesses to collect customer feedback via SMS/QR, incentivize feedback, and manage the process, leveraging PostgreSQL/Prisma for the database and NextAuth.js V5 for authentication.

**Core Technologies & Principles:**

- **Architecture:** Full-Stack Next.js 15 (App Router).
- **Database:** PostgreSQL + Prisma.
- **Frontend:** React, Tailwind CSS, Shadcn/ui, Zustand, TanStack Query, React Hook Form, Zod, lucide-react.
- **Data Fetching Strategy:**
  - **Server Components:** Use **direct server-side data fetching** (calling server functions like Prisma services) for initial page data loads.
  - **Client Components:** Use **TanStack Query (`useQuery`, `useMutation`)** to interact with **API Route Handlers (`app/api`)** for client-initiated data fetching, mutations, and state management.
- **API Layer:** Next.js Route Handlers (`app/api`). **Server Actions excluded for V1.**
- **Authentication:** `next-auth` (Auth.js V5) with Prisma Adapter.
- **Principles:** Server Components default, Client Components (`'use client'`) for interactivity, Route Groups & Layouts, Consistency, Modularity, Reusability, Real-time async loading (spinner) & error feedback (toasts), Page loaders (`loading.js`) deferred.
- **Deferred (V1.0):** Automated Testing, API Documentation.

**Prerequisites (Assumed Done Before Phase 1):**

- Next.js 15 project created (`App Router`, `src` folder, import alias configured).
- Tailwind CSS integrated.
- Shadcn UI initialized (components available, theme configured via global CSS/variables).
- TypeScript disabled.

**Note on Just-in-Time Setup:** Folders (`src/components/auth`, `src/server/services`, etc.) should be created as needed. Packages should be installed just before first use in a phase. Maintain standard project structure (`src/components`, `src/server/services`, `app/api`, etc.).

**Assumptions for AI Agent:**

- Understands project context, tech stack (full-stack Next.js, Prisma, NextAuth.js V5, Shadcn UI, TanStack Query).
- Follows best practices, modularity, uses env vars.
- Uses `'use client'` for interactive components/hooks. Pages remain Server Components.
- Creates directories implicitly.

---

**Phase 1: Project Setup & Core Providers**

_Goal: Set up Prisma, configure NextAuth.js V5, core client-side providers, and foundational UI elements._

- **Task 1.1 (Setup):** Install Prisma: `npm install prisma @prisma/client --save-dev`.
- **Task 1.2 (Setup):** Initialize Prisma for PostgreSQL: `npx prisma init --datasource-provider postgresql`. Configure `DATABASE_URL` in `.env.local`.
- **Task 1.3 (DB):** Define Prisma schemas (`prisma/schema.prisma`) including required `next-auth` models (`User`, `Account`, `Session`, `VerificationToken`) and `Business`.
- **Task 1.4 (DB):** Run initial migration: `npx prisma migrate dev --name init`. Generate client: `npx prisma generate`.
- **Task 1.5 (Setup):** Create Prisma client singleton instance (`src/server/db.js`).
- **Task 1.6 (Auth):** Install `next-auth` & adapter: `npm install next-auth @auth/prisma-adapter`.
- **Task 1.7 (Auth):** Configure `next-auth` (`src/auth.js`). Set up `PrismaAdapter(prisma)`, `providers: []`, `session: { strategy: "jwt" }`. Export `auth`, `handlers`, `signIn`, `signOut`. Configure env vars (`NEXTAUTH_SECRET`, `NEXTAUTH_URL`).
- **Task 1.8 (Auth):** Create the `next-auth` API route handler: `src/app/api/auth/[...nextauth]/route.js` exporting `handlers`.
- **Task 1.9 (FE):** Install client-side providers & utils: `npm install zustand @tanstack/react-query lucide-react`.
- **Task 1.10 (FE):** Create root layout (`src/app/layout.js`) _(Server Component)_. Define base HTML, ensure Tailwind styles apply via `app/global.css`.
- **Task 1.11 (FE):** Create `Providers` component (`src/components/providers/Providers.jsx`) _(Client Component - 'use client')_. Include `SessionProvider`, `QueryClientProvider`.
- **Task 1.12 (FE):** Use `Providers` in root layout wrapping `{children}`.
- **Task 1.13 (FE):** Create reusable `LoadingSpinner` component (`src/components/ui/LoadingSpinner.jsx`).
- **Task 1.14 (FE):** Create `(marketing)` route group & layout (`src/app/(marketing)/layout.js`) _(Server Component)_ - this will define the layout for the homepage and other marketing pages.
- **Task 1.15 (FE):** Create the marketing home page within the group: (`src/app/(marketing)/page.js`) _(Server Component)_ with placeholder content. This handles the `/` route.

- **End of Phase Check:** Prisma connected, NextAuth.js V5 configured. Core providers functional. Marketing homepage viewable at `/`. Reusable loading/toast components ready.

---

**Phase 2: Authentication (Business Owner & Admin)**

_Goal: Implement registration (custom API Route) and login (via `next-auth`), session management, route protection._

- **Task 2.1 (BE/Auth):** Install `bcryptjs`: `npm install bcryptjs`. Implement password hashing utility (`src/server/utils/hashPassword.js`).
- **Task 2.2 (BE/Auth):** Configure `CredentialsProvider` in `src/auth.js` with `authorize` function (using Prisma, bcrypt).
- **Task 2.3 (BE/Service):** Create `userService` (`src/server/services/userService.js`) for `registerUser`.
- **Task 2.4 (BE/API):** Create custom registration Route Handler (`src/app/api/v1/auth/register/route.js`). Use `userService.registerUser`. **(Requires Zod - install if needed: `npm install zod`)**.
- **Task 2.5 (FE):** Install form libraries: `npm install react-hook-form @hookform/resolvers`. _(Zod installed in previous step if needed)_.
- **Task 2.6 (FE):** Create API client function (`src/lib/apiClient/auth.js`) for custom registration endpoint.
- **Task 2.7 (FE):** Create `(auth)` route group & layout (`src/app/(auth)/layout.js`) _(Server Component)_.
- **Task 2.8 (FE):** Implement `RegisterPage` (`src/app/(auth)/register/page.js`) _(Server Component)_ rendering `RegisterForm`.
- **Task 2.9 (FE):** Create `RegisterForm` component (`src/components/auth/RegisterForm.jsx`) _(Client Component - 'use client')_. Uses Shadcn UI, RHF+Zod. **Uses `useMutation` (TanStack) calling `apiClient.register`.** Shows `LoadingSpinner`, uses `toast`.
- **Task 2.10 (FE):** Implement `LoginPage` (`src/app/(auth)/login/page.js`) _(Server Component)_ rendering `LoginForm`.
- **Task 2.11 (FE):** Create `LoginForm` component (`src/components/auth/LoginForm.jsx`) _(Client Component - 'use client')_. Uses Shadcn UI, RHF+Zod. **Calls `signIn('credentials', ...)` from `next-auth/react`**. Handles result (redirect/toast). Manages local loading state.
- **Task 2.12 (Auth):** Create `middleware.js` (root or `src`). Use `auth` helper from `src/auth.js` to protect routes. Configure `matcher`.
- **Task 2.13 (FE):** Create `(app)` route group & layout (`src/app/(app)/layout.js`) _(Server Component)_. Include placeholders. Protected by middleware.
- **Task 2.14 (FE):** Create basic `DashboardPage` (`src/app/(app)/dashboard/page.js`) _(Server Component)_.
- **Task 2.15 (FE):** Update Header/Navbar _(Client Component)_: Use `useSession`. Display conditional UI. Logout uses `signOut()`.

- **End of Phase Check:** Registration/Login functional. Sessions handled. Middleware protects routes. Logout works.

---

**Phase 3: Core Survey Management (Business Owner)**

_Goal: Implement survey creation (API Route + TanStack) and listing (Server Component Fetch)._

- **Task 3.1 (DB):** Define `Survey` schema. Run `prisma migrate dev`, `prisma generate`.
- **Task 3.2 (BE/Service):** Create/Update `surveyService` (`src/server/services/surveyService.js`) for `createSurvey`, `getSurveysByOwnerId`, `getSurveyById`.
- **Task 3.3 (BE/API):** Create authenticated Route Handlers (`src/app/api/v1/surveys/...`) for POST (create), GET (list), GET `[surveyId]` (single). Use `auth()`.
- **Task 3.4 (FE):** Create `apiSurveys` client functions (`src/lib/apiClient/surveys.js`).
- **Task 3.5 (FE):** Enhance `(app)` layout with `Sidebar`.
- **Task 3.6 (FE):** Implement `Sidebar` component (`src/components/layout/Sidebar.jsx`) _(Client Component)_.
- **Task 3.7 (FE):** Implement `DashboardPage` (`src/app/(app)/dashboard/page.js`) _(Server Component)_.
  - **Data Fetching:** **Directly fetch surveys server-side:** `const session = await auth(); const surveys = await getSurveysByOwnerId(session.user.id);`.
  - Renders `SurveyList`, passing `surveys` prop.
- **Task 3.8 (FE):** Create `SurveyList` component (`src/components/dashboard/SurveyList.jsx`) _(Server Component)_. Receives/displays `surveys` prop. Includes `Link` to create.
- **Task 3.9 (FE):** Implement `CreateSurveyPage` (`src/app/(app)/surveys/create/page.js`) _(Server Component)_ rendering `CreateSurveyForm`.
- **Task 3.10 (FE):** Create `CreateSurveyForm` component (`src/components/surveys/CreateSurveyForm.jsx`) _(Client Component - 'use client')_. Uses RHF+Zod, Shadcn UI. **Uses `useMutation` (TanStack) calling `apiClient.createSurvey`**. Shows `LoadingSpinner`, uses `toast`. Redirects.
- **Task 3.11 (FE):** Implement `SurveyDetailPage` (`src/app/(app)/surveys/[surveyId]/page.js`) _(Server Component)_.
  - **Data Fetching:** **Fetch survey details server-side:** `const session = await auth(); const survey = await getSurveyById(params.surveyId, session.user.id);`.
  - Renders child components passing `survey` data.
- **Task 3.12 (FE):** Create `SurveyDetails` component (`src/components/surveys/SurveyDetails.jsx`) _(Server Component)_.

- **End of Phase Check:** Survey listing (server-fetched) and creation (client mutation -> API route) functional. Detail page loads server-side.

---

**Phase 4: Consumer Survey Form**

_Goal: Allow public survey viewing/submission via public API Routes + TanStack Query._

- **Task 4.1 (DB):** Define `SurveyResponse` schema. Run `prisma migrate dev`, `prisma generate`.
- **Task 4.2 (BE/Service):** Create `responseService` (`src/server/services/responseService.js`) for `submitResponse`. Add `getSurveyForPublicView` to `surveyService`.
- **Task 4.3 (BE/API):** Create public Route Handlers under `src/app/api/v1/public/surveys/` (GET `view/[surveyId]`, POST `submit/[surveyId]`).
- **Task 4.4 (FE):** Create `apiPublicSurveys` client functions.
- **Task 4.5 (FE):** Implement `PublicSurveyPage` (`src/app/feedback/[surveyId]/page.js`) _(Server Component)_. Render `SurveyForm`. Use minimal `(feedback)` layout group.
- **Task 4.6 (FE):** Create `SurveyForm` component (`src/components/feedback/SurveyForm.jsx`) _(Client Component - 'use client')_. Props: `surveyId`, `smsIdentifier?`. **Uses `useQuery` (TanStack) calling `apiClient.getSurveyStructure`**. Renders form. **Uses `useMutation` (TanStack) calling `apiClient.submitResponse`**. Shows success state. Shows `LoadingSpinner`, uses `toast`.

- **End of Phase Check:** Public survey form fetches structure and submits responses.

---

**Phase 5: QR Code Feedback Collection**

_Goal: Generate QR codes via authenticated API Route + TanStack Query._

- **Task 5.1 (BE):** Install `qrcode`: `npm install qrcode`.
- **Task 5.2 (BE/API):** Create authenticated Route Handler GET `/api/v1/surveys/[surveyId]/qrcode/route.js`. Use `auth()`. Generate URL, use `qrcode` pkg, return data URL.
- **Task 5.3 (BE/Service):** Ensure `responseService` sets `collectionMethod: 'QR_CODE'`.
- **Task 5.4 (FE):** Add `getSurveyQrCode` to `apiSurveys` client.
- **Task 5.5 (FE):** Create `QrCodeGenerator` component (`src/components/surveys/QrCodeGenerator.jsx`) _(Client Component - 'use client')_. Render on `SurveyDetailPage`. Props: `surveyId`. **Uses `useQuery` (TanStack, on click) calling `apiClient.getSurveyQrCode`**. Displays QR in Dialog. Shows loading, uses `toast`.

- **End of Phase Check:** QR codes generated and displayed.

---

**Phase 6: SMS Feedback Collection & Tracking**

_Goal: Send SMS via API Route + TanStack, handle SMS links, track status via API Route + TanStack._

- **Task 6.1 (BE):** Install SMS SDK (e.g., `npm install twilio`). Create/Configure `smsService`. Use env vars.
- **Task 6.2 (DB):** Define `SmsSend` schema. Run `prisma migrate dev`, `prisma generate`.
- **Task 6.3 (BE/API):** Create authenticated Route Handler POST `/api/v1/surveys/[surveyId]/send-sms/route.js`. Use `auth()`. Generate ID/URL. Create `SmsSend` record. Call `smsService`.
- **Task 6.4 (BE/API):** Create public Route Handler GET `/api/v1/public/feedback/sms/[identifier]/route.js`. Find `SmsSend`, get `Survey`.
- **Task 6.5 (BE/Service):** Modify `responseService.submitResponse` for SMS submissions.
- **Task 6.6 (BE/API):** Create authenticated Route Handler GET `/api/v1/surveys/[surveyId]/sms-sends/route.js`. Use `auth()`. Fetch `SmsSend` records.
- **Task 6.7 (FE):** Implement `SmsFeedbackPage` (`src/app/feedback/sms/[identifier]/page.js`) _(Server Component)_. Render `SurveyForm` passing identifier. Use `(feedback)` layout.
- **Task 6.8 (FE):** Add `sendSms`, `getSmsSends` to `apiSurveys` client.
- **Task 6.9 (FE):** Create `SmsSendForm` component (`src/components/surveys/SmsSendForm.jsx`) _(Client Component - 'use client')_. Render on `SurveyDetailPage`. **Uses `useMutation` (TanStack) calling `apiClient.sendSms`**. Shows loading, uses `toast`.
- **Task 6.10 (FE):** Create `SmsSendList` component (`src/components/surveys/SmsSendList.jsx`) _(Client Component - 'use client')_. Render on `SurveyDetailPage`. **Uses `useQuery` (TanStack) calling `apiClient.getSmsSends`**. Displays in Table. Shows loading, uses `toast`.

- **End of Phase Check:** SMS sending/feedback/tracking functional.

---

**Phase 7: Incentive Mechanism (Discount Code)**

_Goal: Issue discount codes, verify/redeem via API Routes + TanStack Query._

- **Task 7.1 (DB):** Define `DiscountCode` schema. Run `prisma migrate dev`, `prisma generate`.
- **Task 7.2 (BE/Utils):** Create `generateDiscountCode` utility.
- **Task 7.3 (BE/Service):** Modify `responseService.submitResponse` (SMS) to generate/create `DiscountCode`. Modify API response to return code.
- **Task 7.4 (FE):** Modify `SurveyForm` mutation (SMS) to display returned `discountCode`.
- **Task 7.5 (BE/Service):** Create `discountService` for `verifyCode`, `redeemCode`.
- **Task 7.6 (BE/API):** Create authenticated Route Handlers under `src/app/api/v1/discounts/` (GET `verify/[code]`, POST `redeem/[code]`). Use `auth()`. Protect for BOs.
- **Task 7.7 (FE):** Add `apiDiscounts` client functions.
- **Task 7.8 (FE):** Implement `VerifyCodePage` (`src/app/(app)/verify-code/page.js`) _(Server Component)_ rendering `VerifyCodeForm`.
- **Task 7.9 (FE):** Create `VerifyCodeForm` component (`src/components/dashboard/VerifyCodeForm.jsx`) _(Client Component - 'use client')_. **Uses `useQuery` (TanStack) for verification**. Displays status. **Uses `useMutation` (TanStack) for redemption**. Shows loading, uses `toast`.

- **End of Phase Check:** Discount code lifecycle functional.

---

**Phase 8: Admin Panel**

_Goal: Implement basic admin functionalities using server logic, API Routes, and TanStack Query._

- **Task 8.1 (BE/Setup):** Create first ADMIN user in DB.
- **Task 8.2 (FE):** Create `(admin)` route group & layout (`src/app/(admin)/layout.js`) _(Server Component)_. Ensure ADMIN role access (via middleware or layout `auth()` check).
- **Task 8.3 (BE/Service):** Create `adminService` for user management.
- **Task 8.4 (BE/API):** Create admin Route Handlers (`/api/v1/admin/users/`, `/api/v1/admin/stats`). Protect handlers checking ADMIN role via `auth()`.
- **Task 8.5 (FE):** Add `apiAdmin` client functions.
- **Task 8.6 (FE):** Implement `AdminDashboardPage` (`src/app/(admin)/dashboard/page.js`) _(Server Component)_ rendering `PlatformStats`.
- **Task 8.7 (FE):** Create `PlatformStats` component (`src/components/admin/PlatformStats.jsx`) _(Client Component - 'use client')_. **Uses `useQuery` (TanStack) calling stats endpoint**. Shows loading, uses `toast`.
- **Task 8.8 (FE):** Implement `ManageUsersPage` (`src/app/(admin)/users/page.js`) _(Server Component)_. **Fetch users server-side:** `const session = await auth(); /* check role */ const users = await adminService.getAllBusinessOwners();`. Render `UserManagementTable` passing `users`.
- **Task 8.9 (FE):** Create `UserManagementTable` component (`src/components/admin/UserManagementTable.jsx`) _(Client Component - 'use client')_. Receives `users` prop. Actions **use `useMutation` (TanStack) calling admin API endpoints**. Uses `AlertDialog`. Refreshes list via `queryClient.invalidateQueries(['adminUsers'])` (will require converting user list fetch to `useQuery` if client-side refresh is desired without page reload). Shows loading, uses `toast`.

- **End of Phase Check:** Admin panel functional, displays stats, manages users.

---

**Phase 9: Final Touches & Deployment Prep**

_Goal: Prepare for deployment, add deferred loading UI._

- **Task 9.1 (DB):** Ensure final Prisma schema migrated: `npx prisma migrate deploy`.
- **Task 9.2 (Setup):** Finalize env vars (`.env.local`, `.env.example`).
- **Task 9.3 (FE):** Implement `loading.js` files in key route segments using `LoadingSpinner` or Shadcn `Skeleton`.
- **Task 9.4 (FE):** Review UI consistency, responsiveness.
- **Task 9.5 (General):** Perform thorough manual testing.
- **Task 9.6 (Deployment):** Set up PostgreSQL hosting.
- **Task 9.7 (Deployment):** Set up Next.js hosting (Vercel). Connect Git repo. Configure environment variables. Ensure build command includes `prisma generate`.
- **Task 9.8 (Deployment):** Deploy the application.
- **Task 9.9 (Deployment):** Test the deployed application.

- **End of Phase Check:** Application deployed. Page loading UI implemented. Ready for V1 launch.

---
