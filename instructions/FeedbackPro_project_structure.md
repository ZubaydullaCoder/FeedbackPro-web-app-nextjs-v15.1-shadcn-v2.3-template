# Project Structure: FeedbackPro V1.0

---

**Project Structure Document: FeedbackPro V1.0 (Optimized App Router)**

**(As of: Tuesday, April 15, 2025)**

**1. Introduction**

This document outlines the recommended directory and file structure for the FeedbackPro project. The structure is designed for a **Full-Stack Next.js 15 (App Router)** application, prioritizing modularity, reusability, scalability, and clear separation between server-side and client-side concerns within the unified codebase. It assumes the use of the `src` directory and incorporates route grouping best practices, including placing the primary marketing homepage within its logical group.

**2. Root Directory**

```
feedbackpro/
├── .env.local           # Local environment variables (DB URL, secrets - DO NOT COMMIT)
├── .env.example         # Example environment variables (for setup reference)
├── .gitignore           # Specifies intentionally untracked files git should ignore
├── next.config.mjs      # Next.js configuration file
├── package.json         # Project dependencies and scripts
├── postcss.config.js    # PostCSS configuration (for Tailwind CSS)
├── tailwind.config.js   # Tailwind CSS configuration
├── jsconfig.json        # JavaScript configuration (includes path aliases like @/*)
├── middleware.js        # Next.js middleware (for auth protection)
├── prisma/              # Prisma configuration, schema, and migrations
│   ├── schema.prisma    # Prisma database schema definition
│   └── migrations/      # Database migration history files
└── src/                 # Main application source code directory
    ├── app/
    ├── components/
    ├── lib/
    ├── server/
    ├── store/           # Optional: For client-side global state if needed
    └── styles/          # Optional: For additional global styles
```

**3. `src` Directory**

The main container for the application's source code. Folders and files should be created on a just-in-time basis following this structure.

- **`src/app/`**: Core App Router implementation (details below).
- **`src/components/`**: Reusable React components (UI, shared, features).
- **`src/lib/`**: Shared utilities, hooks, client-side API functions, validators.
- **`src/server/`**: Server-only code (DB client, Auth config, services, server utils).
- **`src/store/`**: (Optional) Zustand stores for purely client-side global UI state.
- **`src/styles/`**: (Optional) Additional global stylesheets.
- **`src/middleware.js`**: (Alternative location) Next.js middleware.

**4. `src/app/` Directory (App Router)**

Handles routing, layouts, pages, and API endpoints. Organized with Route Groups.

```
src/app/
├── global.css               # Global styles, Tailwind directives, Shadcn theme variables
├── layout.js                # Root layout (Server Component, applies to all routes)
├── loading.js               # Root loading UI (Optional)
├── error.js                 # Root error UI (Optional, Client Component)
│
├── (marketing)/             # Route Group for public/marketing pages
│   ├── page.js              # Marketing Homepage (Handles '/' route, Server Component) ## MOVED HERE ##
│   └── layout.js            # Layout for marketing pages (e.g., marketing header/footer)
│   # └── about/             # Example: Other marketing pages
│   #     └── page.js
│
├── (auth)/                  # Route Group for authentication pages
│   ├── layout.js            # Layout for auth pages (e.g., centered card)
│   ├── login/
│   │   └── page.js          # Login Page (Server Component -> renders LoginForm)
│   └── register/
│       └── page.js          # Register Page (Server Component -> renders RegisterForm)
│
├── (app)/                   # Route Group for authenticated user sections (Business Owner)
│   ├── layout.js            # Main app layout (Server Component, includes Sidebar/Header, protected)
│   ├── dashboard/
│   │   ├── page.js          # Dashboard Page (Server Component, server-fetches data)
│   │   └── loading.js       # Loading UI for dashboard
│   ├── surveys/
│   │   ├── create/
│   │   │   └── page.js      # Create Survey Page (Server Component -> renders CreateSurveyForm)
│   │   └── [surveyId]/
│   │       ├── page.js      # Survey Detail Page (Server Component, server-fetches data)
│   │       └── loading.js   # Loading UI for survey detail
│   └── verify-code/
│       └── page.js          # Verify Code Page (Server Component -> renders VerifyCodeForm)
│
├── (admin)/                 # Route Group for admin panel sections
│   ├── layout.js            # Admin layout (Server Component, protected for Admin role)
│   ├── dashboard/
│   │   └── page.js          # Admin Dashboard Page (Server Component -> renders PlatformStats)
│   └── users/
│       ├── page.js          # Manage Users Page (Server Component, server-fetches data)
│       └── loading.js       # Loading UI for user management
│
├── (feedback)/              # Route Group for public feedback forms
│   ├── layout.js            # Minimal layout for feedback forms (Optional)
│   ├── [surveyId]/
│   │   └── page.js          # Public Survey Page (QR Link) (Server Component -> renders SurveyForm)
│   └── sms/
│       └── [identifier]/
│           └── page.js      # Public Survey Page (SMS Link) (Server Component -> renders SurveyForm)
│
└── api/                     # API Route Handlers
    ├── auth/
    │   ├── [...nextauth]/
    │   │   └── route.js     # NextAuth.js handlers
    │   └── v1/
    │       └── auth/
    │           └── register/
    │               └── route.js # Custom registration endpoint
    └── v1/                    # API Versioning (Group other API routes here)
        ├── surveys/         # Endpoints for surveys (CRUD, QR, SMS Send, SMS List)
        ├── public/          # Public endpoints (Survey View, Survey Submit, SMS View)
        ├── discounts/       # Endpoints for discount codes (Verify, Redeem)
        └── admin/           # Endpoints for admin panel (Stats, User Management)

```

_(Note: Specific files within `api/v1/_`subdirectories like`route.js`or`[param]/route.js` follow the Route Handler conventions).\*

**5. `src/components/` Directory**

Reusable React components.

```
src/components/
├── ui/                   # Shadcn UI components + custom primitives (e.g., LoadingSpinner.jsx)
├── shared/               # Components used across multiple major sections (e.g., Footer.jsx)
├── layout/               # Layout-specific components (e.g., Sidebar.jsx, Header.jsx)
├── providers/            # Client-side context provider wrappers (Providers.jsx)
├── auth/                 # Auth components (LoginForm.jsx, RegisterForm.jsx)
├── dashboard/            # Dashboard components (SurveyList.jsx, VerifyCodeForm.jsx)
├── surveys/              # Survey components (CreateSurveyForm.jsx, SurveyDetails.jsx, ...)
├── feedback/             # Feedback form components (SurveyForm.jsx)
└── admin/                # Admin panel components (PlatformStats.jsx, UserManagementTable.jsx)
```

**6. `src/lib/` Directory**

Shared utilities, hooks, client-side logic.

```
src/lib/
├── hooks/                # Custom client-side React hooks (if any)
├── utils/                # General client-side utility functions
├── validators/           # Zod schemas
└── apiClient/            # Client-side fetch functions for internal API routes
```

**7. `src/server/` Directory**

Server-only modules. **Do not import into Client Components.**

```
src/server/
├── db.js                 # Prisma client singleton
├── auth.js               # NextAuth.js V5 configuration
├── services/             # Core business logic (userService.js, surveyService.js, etc.)
└── utils/                # Server-only utilities (hashPassword.js, generateDiscountCode.js)
```

**8. `prisma/` Directory**

Contains `schema.prisma` and `migrations/`.

**9. Configuration Files**

Root directory contains standard config files (`next.config.mjs`, `tailwind.config.js`, `jsconfig.json`, etc.).

**10. Conclusion**

This optimized structure leverages Next.js App Router conventions, including Route Groups, for better organization. It maintains a clear separation between client and server code within the unified project, supporting the chosen full-stack architecture with Prisma and NextAuth.js V5, and promoting maintainability for the FeedbackPro application.
