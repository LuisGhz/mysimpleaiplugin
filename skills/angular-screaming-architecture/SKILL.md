---
name: angular-screaming-architecture
description: In this guide, we outline the Screaming Architecture principles for structuring modern Angular (v19-22+) applications. The focus is on organizing code by business domain rather than technical implementation details, leveraging Standalone Components, Signals, and Zoneless reactivity.
---
# Angular Screaming Architecture Guidelines (v19 - v22+)

You are an expert Angular and TypeScript engineer. When generating files, refactoring, or proposing folder structures for this codebase, you must adhere strictly to **Screaming Architecture** principles tailored for modern Angular applications.

The directory structure must "scream" the **business domain** of the application (e.g., `billing`, `analytics`, `auth`), not technical implementation details.

Take full advantage of modern Angular paradigms: Standalone Components, Signals, Zoneless by default, and the `@Service` decorator (Angular 22+).

---

## 1. Folder & Directory Structure Rules

Organize source files (inside `src/app/`) by **Domain Features** first, then by technical concerns inside each domain.

```text
src/app/
├── core/                 # App-wide providers, global layout, interceptors (app.config.ts, app.routes.ts)
├── components/           # App-wide root/global shared UI (non-domain)
│   └── ui/               # Base UI components (e.g., Spartan UI, Angular Aria, or Material)
├── features/             # Business Domains ("Screaming" modules)
│   ├── auth/
│   ├── billing/
│   └── dashboard/
│       ├── api/          # Domain API calls (using `httpResource`, `rxResource` or `HttpClient`)
│       ├── components/   # Domain-specific Standalone UI components (OnPush by default)
│       ├── services/     # Feature-specific logic & state management (using Signals, @Service)
│       ├── models/       # Feature-specific TypeScript interfaces/types
│       ├── utils/        # Feature-specific helpers
│       └── pages/        # Feature-specific routable pages (Smart components)
├── pages/                # (Optional) Root-level view composition if not using feature-based routing
└── shared/               # Domain-agnostic utilities, global types, shared pipes/directives

```

---

## 2. Structural Layer Responsibilities

### `features/[domain]/` (Core Domain Architecture)

* Every distinct business module lives inside `src/app/features/[feature-name]/`.
* **`components/`**: Place UI components that belong *exclusively* to this business domain. They must be Standalone Components.
* **`services/`**: Feature-level state and logic. Use the `@Service()` decorator (v22) or `@Injectable({ providedIn: 'root' })`. Rely on Signals for state over RxJS where possible.

### `pages/` (Views)

* Responsible solely for routing and view-level composition.
* Route components inside `pages/` or `features/[domain]/pages/` should be lightweight wrappers that assemble domain components from `features/`.
* Do not put heavy business logic or deep UI markup inside `pages/`.
* Every page component should be suffixed with `Page` or `Component` (e.g., `BillingHistoryPage`).

### `components/` (Root & UI Base)

* **`src/app/components/ui/`**: Base UI elements (buttons, dialogs, inputs). Highly reusable.
* **`src/app/components/`**: Non-domain, app-wide structural components (e.g., `Navbar`, `SidebarLayout`).

### `shared/` (Shared Helpers)

* Cross-cutting code that has zero knowledge of any specific business domain (e.g., `date-formatter`, global custom directives, validators).

> **Rule:** Keep logic close to services, components, and domain modules. Do not over-abstract with Clean Architecture application services unless strictly necessary. Embrace Angular's DI (Dependency Injection).

---

## 3. Import Path Conventions & Aliases

Always use absolute imports (configured in `tsconfig.json`) via path aliases starting with `@app/` (or `@/`).

* **Base UI Components:** `@app/components/ui/[component-name]`
* **Root / Global Components:** `@app/components/[component-name]`
* **Domain Feature Exports:** `@[domain]` // e.g., `@billing`
* **Page / View Exports:** `@pages/[page-name]`
* **Shared Utilities:** `@shared/[util-name]`

### Example Imports:

```typescript
// Base UI
import { ButtonComponent } from "@app/components/ui/button.component";

// Domain-Specific Implementation
import { InvoiceListComponent } from "@billing/components/invoice-list.component";
import { BillingStateService } from "@billing/services/billing-state.service";

// Shared Utilities & Global Components
import { HeaderComponent } from "@app/components/header.component";
import { formatDate } from "@shared/utils/date";

```

---

## 4. Code & Module Encapsulation Rules

1. **Public API (Barrel Exports):** Each feature folder must expose a top-level `index.ts` (or `public-api.ts`) acting as its public interface.

```typescript
// src/app/features/billing/index.ts
export { InvoiceListComponent } from "./components/invoice-list/invoice-list.component";
export { BillingStateService } from "./services/billing-state.service";
export { billingRoutes } from "./pages/billing.routes";
export type { Invoice } from "./models/invoice.interface";

```

2. **Strict Encapsulation:** Other features must only import from `@[domain]` (e.g., `@billing`), never directly deep-importing internal subfolders.
3. **Domain Independence:** A feature folder should avoid importing directly from another feature folder. Cross-feature communication happens via `pages/` composition, shared services, or event buses.
4. **Angular v19+ Standards:**
* Use **Zoneless** execution (avoid manual `zone.js` triggers).
* Rely on **Control Flow** syntax (`@if`, `@for`, `@defer`) in templates.
* Favor **`httpResource`** or **`rxResource`** for async data fetching instead of heavy manual RxJS subscriptions.
5. **Angular 22+ Standards:**
* Prefer **Signal Forms** over legacy Reactive Forms when building forms.
