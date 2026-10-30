# AGENTS.md — optimus-image-cropper

Canvas-basierter Angular Image Cropper (`OicCropper`) auf Basis von **Optimus UI**
(`@openng/optimus-ui`, PrimeNG-kompatibler Fork).

**Nur Regeln, keine Entscheidungen:** Diese Datei enthält ausschließlich Regeln — Anweisungen, die für jede Arbeit in diesem Repo gelten. Dauerhafte Entscheidungen, Begründungen, Messwerte und Historie gehören nicht hierher, sondern in `docs/`/`features/` bzw. die Projekt-Doku; offene Punkte nach `AGENTS.todo.md`; Erledigtes in die Git-Historie.

## Tech-Stack

- Nx Monorepo + **pnpm** (Node >= 26, pnpm 11.23.0)
- **Angular 22**: Optimus UI gibt die Angular-Version vor — keine eigene Angular-Version vorziehen
- Library: `packages/optimus-image-cropper` (ng-packagr) → npm `@all-the.rest/optimus-image-cropper`
- Demo: `apps/demo` (Aura-Theme aus `@openng/optimus-ui-themes`); E2E: `apps/demo-e2e` (Playwright)
- Tests: **Vitest** (`@analogjs/vitest-angular`), node-canvas (`canvas`) für Canvas-Tests
- i18n-Strings (Aria/Buttons) aus **`@openng/optimus-ui-locale`**

## Befehle

```bash
pnpm install
pnpm nx build optimus-image-cropper   # Library-Build (dist/packages/optimus-image-cropper)
pnpm nx test optimus-image-cropper    # Unit-Tests (Vitest)
pnpm nx lint optimus-image-cropper    # ESLint
pnpm nx serve demo                    # Demo-App lokal
pnpm nx build demo                    # Demo-Build
pnpm nx e2e demo-e2e                  # Playwright E2E
```

Nach jeder Code-Änderung: build + test + lint der Library ausführen.

## Konventionen

- Prefix: Klassen `Oic*`, Selektoren `oic-*`, DI-Token `OIC_*`, CSS-Vars `--oic-*`
- Standalone Components, Signals (`input()`, `output()`, `model()`, `signal()`, `computed()`, `effect()`), `ChangeDetectionStrategy.OnPush`
- **Keine** `@Input()`/`@Output()`-Dekoratoren, kein RxJS für Zustand/Reaktivität (Signals verwenden)
- **Zoneless**: kein `zone.js` (weder App noch Tests), Demo nutzt `provideZonelessChangeDetection()`; UI-Updates nur über Signals/Template-Events — kein Code, der auf Zone-Ticks vertraut
- E2E: Playwright mit **Chrome Desktop UND Mobile Chrome** (beide Projekte müssen grün sein)
- **Kein Tailwind** (weder Library noch Demo), **kein Angular Material**
- Styling über Optimus-Design-Tokens `var(--p-*)` (Aura-kompatibel), keine `--mat-sys-*`
- Core-Klassen `OicCropperCanvas` / `OicCropperInteraction` bleiben framework-neutral (pure TS)
- SSR-sicher: DOM/Canvas-Zugriffe nur hinter `ensureBrowser()`
- Aria-Labels der Toolbar aus `@openng/optimus-ui-locale` (Token `OIC_CROPPER_INTL`), nicht hartcodieren
- Keine Kommentare im Code, sofern nicht explizit gefordert

## Upstream-Tracking

- Port des Croppers aus `angular-material-extended` (`packages/mat-extended/cropper`); Basis-Commit und ausstehende Upstream-Änderungen stehen in **`docs/UPSTREAM.md`**.
- **Regel:** Bei jedem Übernehmen von Upstream-Änderungen `docs/UPSTREAM.md` aktualisieren (Basis-Commit, Datum, abgehakte Änderungen).

## Release / CI

- CI: `.github/workflows/ci.yml` — test, lint, build, e2e, Demo-Deploy auf GitHub Pages (main)
- Release: Tag `v*` → `.github/workflows/release.yml` → npm publish (`--provenance --access public`)
- Versionierung: Nx release mit `currentVersionResolver: "git-tag"`
- **CHANGELOG.md** muss bei jedem Release mit neuen Notes (Features, Fixes, Breaking Changes) aktualisiert werden, in umgekehrter chronologischer Reihenfolge

## Arbeitsweise des Haupt-Agenten (Orchestrierung)

- Der Build-/Verify-Flow steht im zentralen Skill `build-verify`
  (`agents-skills/.agents/skills/build-verify/SKILL.md`),
  Kernregel `agents-skills/.agents/rules/build-verify.md`: pullen, delegieren,
  Verify-Runde, committen, amend, push + CI beobachten.
- Der Haupt-Agent **steuert nur** (planen, delegieren, Status pflegen) und lässt
  Implementierung und Verifikation von getrennten Subagenten machen.

## Arbeitsliste

Offene Arbeit steht in **`Agents.todo.md`** — dort abhaken, was erledigt ist.

## Gates

Regeln dieser Ebene. Was nur ein Verzeichnis betrifft, gehört in die
`AGENTS.md` **dieses** Verzeichnisses — sie wird geladen, wenn eine Datei
daraus gelesen wird.

Ein Plan in `docs/plans/` beginnt mit `**Status:** Plan | In Arbeit | Done |
Empfehlung, nicht implementiert`.

Die kanonischen Agent-Regeln laufen über `ratchet` (Preset `agent-rules`):
`ratchet check` vor jedem Commit und in der CI.
