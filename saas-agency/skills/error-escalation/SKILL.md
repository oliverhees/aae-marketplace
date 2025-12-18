---
name: error-escalation
description: Eskalationsmatrix für Fehlerbehandlung. Definiert wie Agents mit verschiedenen Fehlertypen umgehen sollen.
---

# Error Escalation Matrix

## Grundprinzip

Nicht jeder Fehler ist gleich. Die richtige Reaktion hängt vom Fehlertyp ab:
- Manche Fehler lösen sich von selbst (Retry)
- Manche brauchen Recovery (Checkpoint)
- Manche brauchen Hilfe (Eskalation)

**Ziel:** Maximale Autonomie bei gleichzeitiger Vermeidung von Zeitverschwendung.

---

## Die 5 Eskalationslevel

### Level 1: Automatischer Retry

**Trigger:**
- Netzwerk-Timeout
- Rate Limit erreicht (429)
- Temporärer API-Fehler (5xx)
- Connection Reset
- DNS-Fehler

**Aktion:**
```
1. Warte [10, 20, 40] Sekunden (exponential backoff)
2. Versuche erneut (max 3x)
3. Bei Erfolg: Weitermachen, kurz in progress.md notieren
4. Bei Misserfolg nach 3x: → Level 2
```

**Kein User-Involvement nötig.**

---

### Level 2: Checkpoint Recovery

**Trigger:**
- Agent-Crash
- Context-Verlust
- Unerwarteter Application State
- Inkonsistente Daten nach Operation
- Memory/Resource-Probleme

**Aktion:**
```
1. Letzten Checkpoint aus progress.md lesen
2. State der geänderten Dateien verifizieren
3. Bei Konsistenz: Ab Checkpoint fortsetzen
4. Bei Inkonsistenz:
   a. Letzten Safety-Checkpoint suchen
   b. Rollback wenn nötig
   c. Ab Safety-Checkpoint neu starten
5. Neuen Start-Checkpoint schreiben mit Hinweis auf Recovery
6. Bei Misserfolg: → Level 3
```

**Kein User-Involvement nötig, aber in issues.md dokumentieren.**

---

### Level 3: Team-Eskalation

**Trigger:**
- Wiederholte Fehler nach Retry + Recovery
- Fehler im Code eines anderen Agents
- Abhängigkeit nicht verfügbar
- Unklare Anforderung im eigenen Bereich
- Technisches Problem das Expertise braucht

**Aktion:**
```
1. Problem in issues.md dokumentieren:
   - Was ist passiert
   - Was wurde bereits versucht
   - Was wird gebraucht

2. Task als `blocked` in tasks.md markieren

3. Relevanten Team-Lead oder Spezialisten informieren:
   - Frontend-Problem → Frontend Lead
   - Backend-Problem → Backend Lead
   - DB-Problem → Supabase Specialist
   - Security-Problem → Security Specialist
   - etc.

4. An anderem Task weiterarbeiten falls möglich

5. Bei keiner Lösung durch Team: → Level 4
```

**Kein User-Involvement, aber PM wird informiert.**

---

### Level 4: C-Level Eskalation

**Trigger:**
- Architektur-Problem (grundlegende Design-Frage)
- Business-Entscheidung nötig (Scope, Priorität)
- Prozess-Problem (Workflow funktioniert nicht)
- Team-übergreifender Konflikt
- Technische Schulden-Entscheidung

**Aktion:**
```
1. Problem detailliert in issues.md dokumentieren

2. Eskalation an passenden C-Level:
   - Architektur/Tech → CTO
   - Business/Scope/Priorität → CEO
   - Prozess/Ressourcen → COO

3. Klare Fragestellung formulieren:
   - Kontext
   - Problem
   - Optionen (wenn bekannt)
   - Empfehlung (wenn vorhanden)

4. Auf Entscheidung warten oder an anderem Task arbeiten

5. Entscheidung in decisions.md dokumentieren
```

**PM koordiniert, User wird informiert.**

---

### Level 5: User-Eskalation

**Trigger:**
- Fehlende Credentials/Zugangsdaten
- Externe Services permanent nicht erreichbar
- Fundamentale Scope-Änderung nötig
- Kosten-Entscheidung erforderlich
- Rechtliche/Compliance-Frage
- Drittanbieter-Abhängigkeit nicht lösbar

**Aktion:**
```
1. Problem klar in issues.md dokumentieren

2. PM informiert User mit:
   - Klare Problembeschreibung
   - Was wird vom User benötigt
   - Optionen (falls vorhanden)
   - Impact auf Projekt (Timeline, Scope)

3. Arbeit am betroffenen Bereich pausiert

4. Nach User-Input:
   - Lösung in decisions.md dokumentieren
   - Task-Status aktualisieren
   - Arbeit fortsetzen
```

**User-Involvement erforderlich.**

---

## Fehler-Kategorisierung

### Retryable Errors (Level 1)

```
- ECONNRESET
- ETIMEDOUT
- ENOTFOUND (temporär)
- HTTP 429 (Rate Limit)
- HTTP 500-599 (Server Error)
- HTTP 408 (Request Timeout)
- "socket hang up"
- "network error"
```

### Recoverable Errors (Level 2)

```
- "context window exceeded"
- "out of memory"
- Unerwarteter State nach Operation
- Datei-Inkonsistenz
- Partial Write/Update
```

### Team-Escalation Errors (Level 3)

```
- Type Error in fremdem Code
- Missing Dependency (npm package, etc.)
- Integration funktioniert nicht
- Test schlägt fehl (nicht selbst geschrieben)
- Performance-Problem
- "Cannot find module X"
```

### C-Level Errors (Level 4)

```
- "Sollen wir X oder Y Architektur nutzen?"
- "Ist Feature X wichtiger als Feature Y?"
- "Der aktuelle Prozess funktioniert nicht weil..."
- "Wir haben technische Schulden in Bereich X"
```

### User-Escalation Errors (Level 5)

```
- "Supabase credentials fehlen"
- "Vercel token ungültig"
- "Stripe API key nicht konfiguriert"
- "Domain nicht erreichbar"
- "Brauchen wir GDPR-Compliance für X?"
- "Budget für Service Y?"
```

---

## Eskalations-Flow Diagramm

```
     FEHLER TRITT AUF
            │
            ▼
   ┌─────────────────┐
   │ Ist es retryable?│
   └────────┬────────┘
            │
     Ja ────┼──── Nein
     │      │      │
     ▼      │      ▼
  Level 1   │   ┌─────────────────┐
  (Retry)   │   │ Ist State       │
     │      │   │ inkonsistent?   │
     │      │   └────────┬────────┘
  Erfolg? ──┤            │
     │      │     Ja ────┼──── Nein
  Ja─┴─Nein │      │     │      │
  │      │  │      ▼     │      ▼
  ▼      ▼  │   Level 2  │   ┌─────────────────┐
Weiter  Level 2 (Recovery) │   │ Brauche ich     │
           │      │        │   │ Team-Hilfe?     │
           │   Erfolg? ────┤   └────────┬────────┘
           │      │        │            │
           │   Ja─┴─Nein   │     Ja ────┼──── Nein
           │   │      │    │      │     │      │
           │   ▼      ▼    │      ▼     │      ▼
           │ Weiter Level 3│   Level 3  │   ┌─────────────────┐
           │               │   (Team)   │   │ Architektur/    │
           │               │      │     │   │ Business?       │
           │               │   Gelöst? ─┤   └────────┬────────┘
           │               │      │     │            │
           │               │   Ja─┴─Nein│     Ja ────┼──── Nein
           │               │   │      │ │      │     │      │
           │               │   ▼      ▼ │      ▼     │      ▼
           │               │ Weiter Level 4   Level 4│   Level 5
           │               │            │   (C-Level)│   (User)
           │               │            │      │     │
           └───────────────┴────────────┴──────┴─────┘
```

---

## Dokumentation in issues.md

### Template für Issue-Dokumentation

```markdown
## Issue #[N]: [Kurzer Titel]

**Erstellt:** [DATUM] von [AGENT]
**Status:** Offen / In Bearbeitung / Eskaliert / Gelöst
**Severity:** CRITICAL / HIGH / MEDIUM / LOW
**Eskalationslevel:** 1-5

### Problem
[Was ist passiert]

### Kontext
[Wo/Wann/Wie ist es aufgetreten]

### Bereits versucht
- [Versuch 1] → [Ergebnis]
- [Versuch 2] → [Ergebnis]

### Benötigt
[Was wird zur Lösung gebraucht]

### Eskaliert an
[Agent/Team/C-Level/User]

### Lösung (wenn gelöst)
[Wie wurde es gelöst]

### Lessons Learned (wenn relevant)
[Was haben wir gelernt]
```

---

## Quick Reference

```
Level 1 (Retry):     Netzwerk, Rate Limits, Timeouts
                     → Warten, 3x versuchen

Level 2 (Recovery):  Crash, State-Problem
                     → Checkpoint lesen, fortsetzen

Level 3 (Team):      Brauche Expertise, fremder Code
                     → Team-Lead/Spezialist fragen

Level 4 (C-Level):   Architektur, Business, Prozess
                     → CTO/CEO/COO entscheidet

Level 5 (User):      Credentials, externe Services, Legal
                     → User muss handeln
```
