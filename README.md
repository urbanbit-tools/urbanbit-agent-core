# Urbanbit Agent Core

**Fork von [OpenClaw](https://github.com/openclaw/openclaw)** für das Urbanbit AI Produkt.

---

## 🎯 Zweck

Dieser Fork dient als Basis für ein eigenes AI-Agent-Produkt. Die Entwicklung divergiert bewusst vom Original OpenClaw, während neue Releases und Features vom Upstream getrackt werden.

---

## 🌳 Branch-Strategie

### `main` (Produktiv-Branch)
- **Deine Custom-Version** von OpenClaw
- Hier entwickelst du Features, entfernst ungenutzte Funktionen, und baust dein Produkt
- Divergiert vom Original → **das ist gewollt!**

### `upstream-sync` (Tracking-Branch)
- **Nur zum Lesen** und Evaluieren
- Trackt `upstream/main` (Original OpenClaw)
- Niemals direkt bearbeiten!
- Verwende diesen Branch um zu sehen, was sich im Original ändert

---

## 🔄 Workflow bei neuen OpenClaw-Releases

### 1. **Release-Monitoring**
- **GitHub Watch aktiviert**: Du bekommst Email-Notifications bei neuen Releases
- Alternativ: Manuell checken auf https://github.com/openclaw/openclaw/releases

### 2. **Upstream aktualisieren**
```bash
cd /home/clawdbot/urbanbit-agent-core

# Fetch alle Updates vom Original
git fetch upstream --tags

# Wechsle zum Tracking-Branch
git checkout upstream-sync
git pull upstream main
```

### 3. **Änderungen evaluieren**
```bash
# Was ist neu?
git log main..upstream-sync --oneline

# Code-Diff ansehen
git diff main..upstream-sync

# Changelog checken (falls vorhanden)
cat CHANGELOG.md
```

### 4. **Features übernehmen**

**Option A: Kompletten Release mergen** (wenn du viel übernehmen willst)
```bash
git checkout main
git merge upstream-sync

# Konflikte lösen für Teile, die du angepasst hast
# Editor öffnet automatisch → Konflikte manuell lösen
git add .
git commit -m "Merge OpenClaw release X.Y.Z"
git push origin main
```

**Option B: Einzelne Features cherry-picken** (empfohlen!)
```bash
# Interessante Commits identifizieren
git log main..upstream-sync --oneline | grep "feat:"

# Einzelne Commits übernehmen
git checkout main
git cherry-pick <commit-hash>
git push origin main
```

**Option C: Nichts übernehmen** (auch okay!)
- Wenn das Release nichts Relevantes enthält → einfach weitermachen

---

## 🛠️ Eigene Entwicklung

### Feature-Branch erstellen
```bash
git checkout main
git checkout -b feature/my-awesome-feature

# ... entwickeln ...
git add .
git commit -m "feat: Add awesome feature"
git push origin feature/my-awesome-feature
```

### Pull Request erstellen
- Gehe zu https://github.com/urbanbit-tools/urbanbit-agent-core/pulls
- "New Pull Request" → `feature/my-awesome-feature` → `main`
- Review → Merge

### Direkt auf `main` pushen (für kleine Fixes)
```bash
git checkout main
# ... änderungen ...
git commit -am "fix: Quick bugfix"
git push origin main
```

---

## 📋 Remotes

```bash
origin    → https://github.com/urbanbit-tools/urbanbit-agent-core.git  (dein Fork)
upstream  → https://github.com/openclaw/openclaw.git                    (Original)
```

Check mit:
```bash
git remote -v
```

---

## 🔍 Nützliche Git-Commands

```bash
# Status checken
git status

# Welcher Branch?
git branch

# Upstream-Updates holen (ohne mergen)
git fetch upstream

# Letzte 10 Commits
git log -10 --oneline

# Unterschiede zwischen Branches
git diff main..upstream-sync

# Tag-Liste (Releases)
git tag -l
```

---

## 📦 Installation & Setup

Siehe Original [OpenClaw Docs](https://docs.openclaw.ai) für Installation.

**Wichtig:** Dieser Fork kann von den Installations-Schritten abweichen, sobald Custom-Features hinzukommen.

---

## 🧪 Testing

```bash
# Dependencies installieren
pnpm install

# Tests laufen lassen
pnpm test

# Dev-Server starten
pnpm dev
```

---

## 📝 Lizenz

Dieser Fork behält die Lizenz des Original-Projekts:
- Siehe [LICENSE](LICENSE) für Details
- Credits gehen an das [OpenClaw Team](https://github.com/openclaw/openclaw/graphs/contributors)

---

## 🙏 Credits

Basiert auf [OpenClaw](https://github.com/openclaw/openclaw) – danke an das gesamte Team für die Basis! 🦞
