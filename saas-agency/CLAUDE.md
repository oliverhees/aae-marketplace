# SaaS Agency Plugin

Du hast Zugriff auf eine vollständige AI-Agentur mit **38 spezialisierten Agents** für SaaS-Entwicklung.

## Wann Agents verwenden

**WICHTIG:** Wenn der User nach SaaS-Entwicklung, Web-Apps, oder Features fragt, nutze die passenden Agents!

### Agent-Übersicht nach Aufgabe

| Aufgabe | Agent(s) |
|---------|----------|
| Projektplanung, Vision | `ceo`, `project-manager` |
| Technische Architektur | `cto`, `backend-lead`, `frontend-lead` |
| Datenbank, Supabase | `supabase-specialist`, `backend-developer` |
| UI/UX Design | `ux-designer`, `ui-designer` |
| Frontend-Entwicklung | `frontend-lead`, `frontend-developer`, `nextjs-specialist` |
| Backend/API | `backend-lead`, `backend-developer`, `api-architect` |
| Authentication | `auth-expert`, `supabase-specialist` |
| Payments/Stripe | `stripe-specialist` |
| AI/Chat Features | `ai-integration-specialist`, `qdrant-specialist` |
| Deployment | `vercel-specialist`, `devops-engineer`, `coolify-specialist` |
| Testing | `qa-lead`, `testing-specialist`, `visual-qa` |
| Security | `security-specialist`, `privacy-expert` |
| Performance | `performance-engineer`, `redis-specialist` |
| SEO/Content | `seo-specialist`, `content-strategist`, `copywriter` |
| Analytics | `analytics-specialist`, `data-analyst` |

## So verwendest du Agents

Starte den passenden Agent mit dem Task-Tool:

```
Task(subagent_type="stripe-specialist", prompt="Implementiere Subscription-System mit Pro und Enterprise Plan")
```

### Mehrere Agents parallel (Swarm Mode)

Bei unabhängigen Aufgaben starte mehrere Agents gleichzeitig:

```
Task(subagent_type="frontend-developer", prompt="Login-Formular UI")
Task(subagent_type="backend-developer", prompt="Auth API Routes")
Task(subagent_type="email-specialist", prompt="Welcome Email Template")
```

## Memory Bank

Die Agents nutzen eine gemeinsame Memory Bank für Dokumentation:

```
.claude/memory-bank/
├── project-brief.md    # Projektvision, Ziele, MVP
├── architecture.md     # Technische Architektur
├── tasks.md            # Aktuelle Aufgaben
├── progress.md         # Fortschritt
├── decisions.md        # Architektur-Entscheidungen
└── issues.md           # Bekannte Probleme
```

**Erstelle diese Dateien** wenn du ein neues Projekt startest!

## Workflow für neue Projekte

1. **CEO Agent** → Definiert Vision und MVP-Scope
2. **CTO Agent** → Plant technische Architektur
3. **Project Manager** → Erstellt Tasks und koordiniert
4. **Spezialisierte Agents** → Implementieren Features

## Tech Stack

Die Agents sind optimiert für:
- **Next.js 16** (App Router, Server Components)
- **Supabase** (Auth, Database, RLS, Real-time)
- **ShadcnUI** (UI Components)
- **Vercel** (Deployment)
- **Playwright** (Testing)

## Beispiel-Prompts

- "Plane eine SaaS-App für Zeiterfassung" → CEO + CTO
- "Implementiere User Authentication" → auth-expert + supabase-specialist
- "Baue eine Pricing-Seite mit Stripe" → stripe-specialist + frontend-developer
- "Optimiere die Performance" → performance-engineer + redis-specialist
- "Mach ein Security Audit" → security-specialist + code-reviewer
