---
name: react-screaming-architecture
description: In this guide, we outline the Screaming Architecture principles for structuring React applications. The focus is on organizing code by business domain rather than technical implementation details, ensuring clarity and maintainability.
---
# React Screaming Architecture Guidelines

You are an expert React and TypeScript engineer. When generating files, refactoring, or proposing folder structures for this codebase, you must adhere strictly to **Screaming Architecture** principles tailored for React applications.

The directory structure must "scream" the **business domain** of the application (e.g., `billing`, `analytics`, `auth`), not technical implementation details.

---

## 1. Folder & Directory Structure Rules

Organize source files (`src/`) by **Domain Features** first, then by technical concerns inside each domain.

```text
src/
├── app/                  # Application routing, providers, global layout
├── components/           # App-wide root/global shared UI (non-domain)
│   └── ui/               # shadcn/ui components (@/components/ui/...)
├── features/             # Business Domains ("Screaming" modules)
│   ├── auth/
│   ├── billing/
│   └── dashboard/
│       ├── api/          # Domain API services & query hooks
│       ├── components/   # Domain-specific UI components
│       ├── hooks/        # Feature-specific hooks
│       ├── types/        # Feature-specific TypeScript interfaces
│       └── utils/        # Feature-specific helpers
│       └── pages/        # Feature-specific pages
├── pages/                # Route targets / Views (maps routes to domain features)
└── shared/               # Domain-agnostic utilities, global types, shared hooks

```

---

## 2. Structural Layer Responsibilities

### `features/[domain]/` (Core Domain Architecture)

* Every distinct business module lives inside `src/features/[feature-name]/`.
* **`components/`**: Place UI components that belong *exclusively* to this business domain.

### `pages/` (Views)

* Responsible solely for routing and view-level composition.
* Route components inside `src/pages/` should be lightweight wrappers that assemble domain components from `src/features/`.
* Do not put heavy business logic or deep UI markup inside `pages/`.
* Every page inside features should be ended with `Page` (e.g., `BillingHistoryPage`, `DashboardPage`, `AuthPage`).

### `components/` (Root & UI Base)

* **`src/components/ui/`**: Base UI elements generated or inspired by `shadcn/ui` (buttons, dialogs, inputs).
* **`src/components/`**: Non-domain, app-wide structural components (e.g., `Navbar`, `Footer`, `SidebarLayout`).

### `shared/` (Shared Helpers)

* Cross-cutting code that has zero knowledge of any specific business domain (e.g., `date-formatter`, `api-client`, global custom hooks).

> **Rule:** Do NOT use "use cases" or clean architecture application service abstractions. Keep logic close to hooks, components, and domain modules.

---

## 3. Import Path Conventions (`shadcn` & Aliases)

Always use absolute imports (if available) via path aliases starting with `@/`.

* **shadcn UI Components:** `@/components/ui/[component-name]`
* **Root / Global Components:** `@/components/[component-name]`
* **Domain Feature Exports:** `@/features/[domain]`
* **Page / View Exports:** `@/pages/[page-name]`
* **Shared Utilities:** `@/shared/[util-name]`

### Example Imports:

```typescript
// Base UI (shadcn pattern)
import { Button } from "@/components/ui/button";
import { Dialog, DialogContent } from "@/components/ui/dialog";

// Domain-Specific Component
import { InvoiceList } from "@/features/billing/components/InvoiceList";
import { useBillingHistory } from "@/features/billing/hooks/useBillingHistory";

// Shared Utilities & Global Components
import { Header } from "@/components/Header";
import { formatDate } from "@/shared/utils/date";

```

---

## 4. Code & Module Encapsulation Rules

1. **Public API (Barrel Exports):** Each feature folder must expose a top-level `index.ts` acting as its public interface.
```typescript
// src/features/billing/index.ts
export { InvoiceList } from "./components/InvoiceList";
export { useBillingHistory } from "./hooks/useBillingHistory";
export type { Invoice } from "./types";

```


2. **Strict Encapsulation:** Other features must only import from `@/features/[domain]`, never directly deep-importing internal subfolders like `@/features/[domain]/components/internal-item`.
3. **Domain Independence:** A feature folder should avoid importing directly from another feature folder. Cross-feature communication should happen via `pages/` composition or shared state/events.