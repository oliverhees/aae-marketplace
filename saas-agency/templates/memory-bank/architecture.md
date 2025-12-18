# Architecture: [PROJEKTNAME]

> Erstellt: [DATUM]
> Letzte Aktualisierung: [DATUM]
> Verantwortlich: CTO, API Architect

---

## Tech Stack

### Core

| Bereich | Technologie | Version | Begründung |
|---------|-------------|---------|------------|
| Framework | Next.js | 16.x | App Router, RSC, Performance |
| Language | TypeScript | 5.x | Type Safety, DX |
| Database | Supabase (PostgreSQL) | Latest | Auth, Realtime, Storage integriert |
| UI | ShadcnUI | Latest | Customizable, a11y, Tailwind |
| Styling | Tailwind CSS | 3.x | Utility-first |

### Infrastructure

| Bereich | Technologie | Begründung |
|---------|-------------|------------|
| Hosting | Vercel | Edge, Preview Deployments |
| Database Hosting | Supabase Cloud / Coolify | Managed PostgreSQL |
| CDN | Vercel Edge Network | Global, schnell |

### Services (je nach Features)

| Service | Technologie | Wann benötigt |
|---------|-------------|---------------|
| Auth | Supabase Auth | Immer |
| Storage | Supabase Storage | File Uploads |
| Realtime | Supabase Realtime | Live Updates |
| Vector DB | Qdrant | AI/Search Features |
| Payments | Stripe | Monetarisierung |
| Email | Resend | Transactional Emails |
| Analytics | Posthog | User Analytics |
| Monitoring | Sentry | Error Tracking |
| Caching | Redis/Upstash | Performance |

---

## System-Architektur

```
┌─────────────────────────────────────────────────────────────┐
│                         CLIENT                               │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                    Next.js App                       │   │
│  │  ┌───────────┐  ┌───────────┐  ┌───────────┐       │   │
│  │  │  Pages    │  │Components │  │  Hooks    │       │   │
│  │  │(App Router│  │(ShadcnUI) │  │           │       │   │
│  │  └───────────┘  └───────────┘  └───────────┘       │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                       SERVER                                 │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              Next.js API / Server Actions            │   │
│  │  ┌───────────┐  ┌───────────┐  ┌───────────┐       │   │
│  │  │API Routes │  │  Server   │  │Middleware │       │   │
│  │  │           │  │  Actions  │  │  (Auth)   │       │   │
│  │  └───────────┘  └───────────┘  └───────────┘       │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      SERVICES                                │
│  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌─────────┐ │
│  │ Supabase  │  │  Stripe   │  │  Resend   │  │ Qdrant  │ │
│  │(DB, Auth, │  │(Payments) │  │ (Email)   │  │(Vectors)│ │
│  │ Storage)  │  │           │  │           │  │         │ │
│  └───────────┘  └───────────┘  └───────────┘  └─────────┘ │
└─────────────────────────────────────────────────────────────┘
```

---

## Ordnerstruktur

```
project/
├── app/                        # Next.js App Router
│   ├── (auth)/                 # Auth Route Group
│   │   ├── login/
│   │   ├── register/
│   │   └── layout.tsx
│   ├── (dashboard)/            # Protected Route Group
│   │   ├── layout.tsx          # Dashboard Layout (Sidebar)
│   │   ├── page.tsx            # Dashboard Home
│   │   └── [feature]/          # Feature Pages
│   ├── (marketing)/            # Public Pages
│   │   ├── page.tsx            # Landing Page
│   │   └── pricing/
│   ├── api/                    # API Routes
│   │   ├── auth/
│   │   └── webhooks/
│   ├── layout.tsx              # Root Layout
│   └── globals.css
├── components/
│   ├── ui/                     # ShadcnUI Components
│   ├── layout/                 # Layout Components
│   ├── features/               # Feature-specific Components
│   └── shared/                 # Shared Components
├── lib/
│   ├── supabase/              # Supabase Clients
│   │   ├── client.ts          # Browser Client
│   │   ├── server.ts          # Server Client
│   │   └── middleware.ts      # Auth Middleware
│   ├── types/                 # TypeScript Types
│   │   ├── api.ts             # API Contracts
│   │   └── database.ts        # Supabase Types
│   ├── utils/                 # Utility Functions
│   └── hooks/                 # Custom Hooks
├── supabase/
│   ├── migrations/            # Database Migrations
│   └── seed.sql               # Seed Data
├── public/                    # Static Assets
└── tests/                     # Test Files
```

---

## API Design

### API Contracts

Alle API Contracts werden in `lib/types/api.ts` definiert:

```typescript
// Beispiel-Struktur
export interface ApiResponse<T> {
  data: T | null;
  error: string | null;
}

export interface User {
  id: string;
  email: string;
  name: string;
  avatar_url?: string;
  created_at: string;
}

// Weitere Types hier...
```

### Endpoint-Konventionen

| Pattern | Methode | Beschreibung |
|---------|---------|--------------|
| `/api/[resource]` | GET | Liste abrufen |
| `/api/[resource]` | POST | Neu erstellen |
| `/api/[resource]/[id]` | GET | Einzeln abrufen |
| `/api/[resource]/[id]` | PATCH | Aktualisieren |
| `/api/[resource]/[id]` | DELETE | Löschen |

---

## Database Schema

### Tables

```sql
-- Beispiel-Struktur (wird vom Supabase Specialist ausgefüllt)

-- Users (extends Supabase auth.users)
CREATE TABLE public.profiles (
  id UUID REFERENCES auth.users(id) PRIMARY KEY,
  name TEXT,
  avatar_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Weitere Tables hier...
```

### Row Level Security (RLS)

```sql
-- Beispiel RLS Policies

-- Users can read their own profile
CREATE POLICY "Users can read own profile"
  ON public.profiles FOR SELECT
  USING (auth.uid() = id);

-- Weitere Policies hier...
```

---

## Authentication Flow

```
1. User besucht /login
2. User gibt Credentials ein
3. Supabase Auth verifiziert
4. Session Cookie wird gesetzt
5. Middleware prüft Session bei geschützten Routes
6. User wird zu /dashboard weitergeleitet
```

### Protected Routes

- `/dashboard/*` - Erfordert Auth
- `/api/*` - Erfordert Auth (außer Webhooks)
- `/settings/*` - Erfordert Auth

---

## Entscheidungen

| Datum | Entscheidung | Alternativen | Begründung | Entscheider |
|-------|--------------|--------------|------------|-------------|
| [DATUM] | [Was] | [Alternativen] | [Warum] | [Wer] |

---

## Sicherheitskonzept

### Authentication
- Supabase Auth mit JWT
- Session-basiert
- Optional: MFA

### Authorization
- Row Level Security (RLS) in Supabase
- Middleware für Route Protection
- API Route Guards

### Data Protection
- Alle Daten verschlüsselt at rest (Supabase)
- HTTPS only
- Sensitive Data nicht in Client

---

## Performance-Strategie

### Caching
- [ ] Static Pages via ISR
- [ ] API Response Caching
- [ ] Redis für Session/Data Cache (wenn nötig)

### Optimization
- [ ] Image Optimization (next/image)
- [ ] Code Splitting
- [ ] Lazy Loading

### Monitoring
- [ ] Core Web Vitals Tracking
- [ ] Error Tracking (Sentry)
- [ ] Performance Monitoring

---

## Offene Architektur-Fragen

- [ ] [Frage 1]
- [ ] [Frage 2]
