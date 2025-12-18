---
name: checkpoint-management
description: System für Fortschritts-Checkpoints und Recovery. Ermöglicht Wiederaufnahme der Arbeit nach Unterbrechungen.
---

# Checkpoint Management

## Warum Checkpoints?

Checkpoints sind kritisch für:
1. **Recovery** - Bei Agent-Crash kann ab letztem Checkpoint fortgesetzt werden
2. **Transparenz** - Alle sehen den aktuellen Stand
3. **Koordination** - Andere Agents wissen was du tust
4. **Debugging** - Bei Problemen kann man nachvollziehen was passiert ist

---

## Wann Checkpoint erstellen?

### PFLICHT-Checkpoints

| Situation | Checkpoint-Typ |
|-----------|----------------|
| Agent-Start | Start-Checkpoint |
| Alle 10 Minuten | Intervall-Checkpoint |
| Nach Teilaufgabe | Progress-Checkpoint |
| Vor risikoreicher Operation | Safety-Checkpoint |
| Bei Pause/Unterbrechung | Pause-Checkpoint |
| Nach Task-Abschluss | Completion-Checkpoint |
| Bei Agent-Stop | Final-Checkpoint |

### Optionale Checkpoints

- Nach erfolgreicher Integration
- Nach wichtigen Entscheidungen
- Vor/Nach externen API-Calls
- Bei Meilenstein-Erreichung

---

## Checkpoint-Formate

### Standard-Checkpoint

```markdown
### [YYYY-MM-DD HH:MM] - [Agent Name]
**Task:** [Aktueller Task aus tasks.md]
**Fortschritt:** [0-100]%
**Letzte Aktion:** [Was wurde gerade abgeschlossen]
**Nächster Schritt:** [Was kommt als nächstes]
**Geänderte Dateien:**
- path/to/file1.ts
- path/to/file2.tsx
```

### Start-Checkpoint

```markdown
### [YYYY-MM-DD HH:MM] - [Agent Name] - START
**Task:** [Task aus tasks.md]
**Kontext gelesen:**
- [x] project-brief.md
- [x] tasks.md
- [x] progress.md
- [x] issues.md
**Abhängigkeiten:** [Andere Agents/Tasks von denen ich abhänge]
**Geplante Schritte:**
1. [Schritt 1]
2. [Schritt 2]
3. [Schritt 3]
```

### Safety-Checkpoint (vor risikoreicher Operation)

```markdown
### [YYYY-MM-DD HH:MM] - [Agent Name] - SAFETY
**Vor Operation:** [Was wird gemacht]
**Risiko:** [Was könnte schiefgehen]
**Rollback-Plan:** [Wie kann ich zurückrollen]
**Betroffene Dateien:**
- [datei] - [Was wird geändert]
**Letzte funktionierende Version:** [Commit/Checkpoint-Referenz]
```

### Completion-Checkpoint

```markdown
### [YYYY-MM-DD HH:MM] - [Agent Name] - COMPLETE
**Task abgeschlossen:** [Task-Name]
**Ergebnis:** [Was wurde erreicht]
**Erstellte/Geänderte Dateien:**
- [datei] - [neu/geändert]
**Tests:** [Bestanden/Nicht getestet/Fehlgeschlagen]
**Nächste Tasks:** [Was sollte als nächstes passieren]
**Übergabe an:** [Welcher Agent/Team]
```

### Final-Checkpoint (bei Agent-Stop)

```markdown
### [YYYY-MM-DD HH:MM] - [Agent Name] - FINAL
**Status bei Stop:** [Arbeitet/Fertig/Blocked]
**Aktueller Task:** [Task-Name]
**Fortschritt:** [X]%
**Unerledigte Arbeit:**
- [Was noch zu tun ist]
**Wichtig für Nachfolger:**
- [Kritische Infos]
**Offene Fragen:**
- [Falls vorhanden]
```

---

## Recovery-Prozess

### Bei Agent-Neustart nach Crash/Unterbrechung

```markdown
1. LESE letzten Checkpoint für diesen Agent aus progress.md

2. PRÜFE Checkpoint-Typ:
   - Standard/Progress → Setze bei "Nächster Schritt" fort
   - Safety → Prüfe ob Operation erfolgreich war
   - Pause → Setze bei letzter Aktion fort

3. VERIFIZIERE geänderte Dateien:
   - Existieren sie?
   - Sind sie konsistent?
   - Bei Inkonsistenz → Safety-Checkpoint suchen

4. AKTUALISIERE Status:
   - Neuen Start-Checkpoint schreiben
   - "Fortgesetzt von Checkpoint [ZEIT]" notieren

5. SETZE Arbeit fort
```

### Bei Inkonsistentem State

```markdown
1. Finde letzten Safety-Checkpoint
2. Prüfe Rollback-Plan
3. Wenn nötig: Rollback durchführen
4. Neu starten ab Safety-Checkpoint
5. In issues.md dokumentieren was passiert ist
```

---

## Checkpoint-Intervall-Logik

```
STANDARD: Alle 10 Minuten

ERHÖHT (alle 5 Minuten):
- Bei komplexen Operationen
- Bei vielen parallelen Änderungen
- Bei kritischem Code (Auth, Payments, etc.)

REDUZIERT (alle 15-20 Minuten):
- Bei einfachen, repetitiven Tasks
- Bei Dokumentationsarbeit
- Bei Review-Tasks
```

---

## Integration mit Error Recovery

```
Bei Fehler:
│
├── Level 1: Retry
│   └── Kein Checkpoint nötig, einfach wiederholen
│
├── Level 2: Checkpoint Recovery
│   ├── Letzten Checkpoint lesen
│   ├── State verifizieren
│   └── Ab Checkpoint fortsetzen
│
├── Level 3: Safety Checkpoint Recovery
│   ├── Letzten Safety-Checkpoint finden
│   ├── Rollback wenn nötig
│   └── Ab Safety-Checkpoint neu starten
│
└── Level 4: Full Recovery
    ├── Task als blocked markieren
    ├── PM informieren
    └── Auf Anweisungen warten
```

---

## Checkpoint-Hygiene

### DO's

- Checkpoints klar und präzise schreiben
- Alle geänderten Dateien auflisten
- Nächste Schritte konkret benennen
- Bei Unsicherheit Safety-Checkpoint machen

### DON'Ts

- Checkpoints überspringen um "Zeit zu sparen"
- Vage Beschreibungen ("einiges gemacht")
- Dateien vergessen aufzulisten
- Zu viele unwichtige Details

---

## Automatische Checkpoint-Erinnerung

Das Plugin erinnert automatisch an Checkpoints via Hook:

```markdown
# Hook: checkpoint-reminder (PreToolUse)

Wenn letzer Checkpoint > 10 Minuten:
→ "ERINNERUNG: Bitte Checkpoint in progress.md erstellen"
```

---

## Quick Reference

```
START:      Start-Checkpoint schreiben
            → Kontext, geplante Schritte

ALLE 10 MIN: Standard-Checkpoint
            → Task, Fortschritt, letzte Aktion, nächster Schritt

VOR RISIKO: Safety-Checkpoint
            → Operation, Risiko, Rollback-Plan

NACH TASK:  Completion-Checkpoint
            → Ergebnis, Tests, Übergabe

BEI STOP:   Final-Checkpoint
            → Status, unerledigte Arbeit, Infos für Nachfolger
```
