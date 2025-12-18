---
name: memory-bank-protocol
description: Pflicht-Protokoll für alle Agents zum Umgang mit der Memory Bank. Muss bei JEDEM Agent-Start befolgt werden.
---

# Memory Bank Protocol

## KRITISCH: Dieses Protokoll ist PFLICHT für JEDEN Agent

Die Memory Bank ist die Single Source of Truth für das gesamte Projekt. Ohne sie verlieren Agents den Kontext und machen redundante oder widersprüchliche Arbeit.

## Memory Bank Struktur

```
{projekt}/.claude/memory-bank/
├── project-brief.md      # Vision, Ziele, Scope, Zielgruppe
├── roadmap.md            # Meilensteine, Phasen, Timeline
├── architecture.md       # Tech-Entscheidungen, System-Design
├── tasks.md              # Aktuelle Tasks pro Agent/Team
├── progress.md           # Live-Status, Checkpoints, Fortschritt
├── decisions.md          # Entscheidungs-Log mit Begründungen
├── issues.md             # Probleme, Blocker, Known Issues
└── team-status.md        # Aktive/Inaktive Agents, Lazy Loading
```

---

## BEI JEDEM AGENT-START - PFLICHT-CHECKLISTE

### Schritt 1: Memory Bank lesen (in dieser Reihenfolge)

```markdown
□ 1. project-brief.md lesen
     → Was bauen wir? Für wen? Was ist der Scope?

□ 2. tasks.md lesen
     → Was ist MEIN aktueller Task?
     → Gibt es Abhängigkeiten zu anderen Agents?

□ 3. progress.md lesen
     → Wo stehen die anderen Agents?
     → Gibt es Checkpoints die ich kennen muss?

□ 4. issues.md lesen
     → Gibt es Blocker die mich betreffen?
     → Gibt es bekannte Probleme in meinem Bereich?

□ 5. team-status.md lesen
     → Welche anderen Agents sind gerade aktiv?
     → Mit wem muss ich mich koordinieren?
```

### Schritt 2: Eigenen Status aktualisieren

```markdown
□ In tasks.md: Meinen Task als `in_progress` markieren
□ In team-status.md: Mich als `Aktiv` eintragen
□ In progress.md: Start-Checkpoint schreiben
```

---

## WÄHREND DER ARBEIT - Kontinuierliche Updates

### Alle 10 Minuten: Checkpoint schreiben

```markdown
In progress.md einen Checkpoint eintragen:

### [TIMESTAMP] - [AGENT NAME]
**Task:** [Aktueller Task]
**Fortschritt:** [X]%
**Letzte Aktion:** [Was wurde gerade abgeschlossen]
**Nächster Schritt:** [Was kommt als nächstes]
**Geänderte Dateien:**
- [datei1.ts]
- [datei2.ts]
```

### Bei wichtigen Entscheidungen: In decisions.md dokumentieren

```markdown
| Datum | Entscheidung | Begründung | Entscheider |
|-------|--------------|------------|-------------|
| [DATUM] | [WAS] | [WARUM] | [WER] |
```

### Bei Problemen: In issues.md dokumentieren

```markdown
### Issue #[N]: [Titel]
- **Agent:** [Wer hat es gefunden]
- **Beschreibung:** [Was ist das Problem]
- **Schwere:** HIGH / MEDIUM / LOW
- **Status:** Offen / In Bearbeitung / Gelöst
- **Workaround:** [Falls vorhanden]
```

---

## NACH TASK-ABSCHLUSS - Pflicht-Updates

```markdown
□ 1. tasks.md: Task als `done` markieren
□ 2. progress.md: Finalen Checkpoint schreiben
□ 3. Nächsten Task aus tasks.md holen ODER PM fragen
□ 4. Falls keine Tasks mehr: In team-status.md als `Idle` markieren
```

---

## MEMORY BANK DATEIEN - Format-Referenz

### project-brief.md

```markdown
# Project Brief: [Projektname]

## Vision
[Ein Satz der das Projekt beschreibt]

## Problem Statement
[Welches Problem lösen wir?]

## Zielgruppe
- Primary: [Hauptzielgruppe]
- Secondary: [Sekundäre Zielgruppe]

## Kernfeatures (MVP - Must Have)
1. [ ] Feature 1
2. [ ] Feature 2

## Nice-to-Have (Post-MVP)
- Feature A
- Feature B

## Explizit NICHT im Scope
- [Was wir bewusst nicht bauen]

## Erfolgskriterien
- [ ] Kriterium 1
- [ ] Kriterium 2

## Tech Stack
- Framework: Next.js 16
- Database: Supabase (self-hosted auf Coolify)
- UI: ShadcnUI
- Deployment: Vercel / Coolify
```

### tasks.md

```markdown
# Tasks

## Aktueller Sprint

### [Team/Agent Name]
| Task | Status | Priorität | Blocker |
|------|--------|-----------|---------|
| [Task] | pending/in_progress/done | HIGH/MEDIUM/LOW | - |

## Backlog
- [ ] [Task]

## Blocked
| Task | Agent | Blocker | Seit |
|------|-------|---------|------|
```

### progress.md

```markdown
# Progress

## Agent Status (Live)

| Agent | Status | Aktueller Task | Fortschritt | Letzter Checkpoint |
|-------|--------|----------------|-------------|-------------------|
| [Agent] | [Status] | [Task] | [X]% | [Zeit] |

## Status-Legende
- 🟢 Arbeitet / Idle
- 🟡 Retry / Warning
- 🔴 Blocked / Error

## Checkpoints Heute

### [Zeit] - [Agent]
[Checkpoint-Details]

## Gesamtfortschritt

[Meilenstein-Progress-Bars]
```

---

## REGELN - NIEMALS BRECHEN

1. **NIEMALS** arbeiten ohne vorher Memory Bank zu lesen
2. **NIEMALS** Tasks vergessen zu updaten
3. **NIEMALS** länger als 10 Minuten ohne Checkpoint
4. **NIEMALS** wichtige Entscheidungen ohne Dokumentation
5. **NIEMALS** Probleme verschweigen - immer in issues.md dokumentieren
6. **IMMER** den eigenen Status aktuell halten
7. **IMMER** prüfen ob andere Agents von meiner Arbeit betroffen sind

---

## QUICK REFERENCE

```
START:     Lies project-brief → tasks → progress → issues → team-status
           Markiere Task als in_progress

ARBEITEN:  Alle 10 Min Checkpoint
           Bei Entscheidungen → decisions.md
           Bei Problemen → issues.md

FERTIG:    Task als done markieren
           Finaler Checkpoint
           Nächsten Task holen oder Idle
```
