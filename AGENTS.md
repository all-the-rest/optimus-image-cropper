# AGENTS.md — optimus-image-cropper

Canvas-basierter Angular Image Cropper (`OicCropper`) auf Basis von **Optimus UI**
(`@openng/optimus-ui`, PrimeNG-kompatibler Fork). Portiert aus
`angular-material-extended` (`packages/mat-extended/cropper`).

## Tech-Stack

- Nx Monorepo + **pnpm** (Node >= 26, pnpm 11.10.0)
- **Angular 22** (Vorgabe durch Optimus UI peerDependency `^22.0.0`); keine eigene Angular-Version vorziehen — Optimus UI 2.x gibt die Angular-Version vor
- Library: `packages/optimus-image-cropper` → npm `@all-the.rest/optimus-image-cropper` (ng-packagr)
- Demo: `apps/demo` (Aura-Theme aus `@openng/optimus-ui-themes`), E2E: `apps/demo-e2e` (Playwright)
- Tests: **Vitest** (`@analogjs/vitest-angular`), node-canvas (`canvas`) für Canvas-Tests
- i18n-Strings (Aria/Buttons): **`@openng/optimus-ui-locale`**

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

- Dieses Projekt ist ein Port des Croppers aus `angular-material-extended`
  (`packages/mat-extended/cropper`). Der **exakte Upstream-Stand** (Basis-Commit)
  und alle seit der Portierung fehlenden Upstream-Änderungen stehen in
  **`docs/UPSTREAM.md`**.
- **Regel:** Bei jedem Übernehmen von Upstream-Änderungen `docs/UPSTREAM.md`
  aktualisieren (Basis-Commit, Datum, abgehakte Änderungen). Ausstehende
  Upstream-Fixes (Stand 2026-08-01): Panning-Fix `3cbc903` (Crop-Rechteck beim
  Hintergrund-Verschieben mitbewegen) und kleinere Anpassungen aus `03ae35e`.

## Release / CI

- CI: `.github/workflows/ci.yml` — test, lint, build, e2e, Demo-Deploy auf GitHub Pages (main)
- Release: Tag `v*` → `.github/workflows/release.yml` → npm publish (`--provenance --access public`)
- Versionierung: Nx release mit `currentVersionResolver: "git-tag"`
- **CHANGELOG.md** muss bei jedem Release mit neuen Notes (Features, Fixes, Breaking Changes) aktualisiert werden, in umgekehrter chronologischer Reihenfolge

## Arbeitsweise des Haupt-Agenten (Orchestrierung)

- Der Haupt-Agent **steuert nur**: planen, delegieren, Status pflegen — er implementiert
  und verifiziert **nichts selbst**
- Jede Umsetzungsarbeit (Code, Tests, Konfiguration) läuft in **Subagenten**;
  unabhängige Aufgaben parallel starten
- Nach **jedem** abgeschlossenen Schritt prüft ein **separater Review-Subagent**,
  ob die Punkte wirklich erledigt sind und die Konventionen dieser Datei eingehalten
  werden (Signals statt `@Input`/`@Output`/RxJS, zoneless, kein Tailwind/Material,
  Prefixe, Tokens, optimus-ui-locale, Chrome Desktop + Mobile in E2E)
- Ergebnis des Reviews: Checkboxen in `Agents.todo.md` abhaken oder so umschreiben,
  dass der tatsächliche Stand sichtbar ist; veraltete Angaben in `AGENTS.md` korrigieren
- **Push-Gate:** `git push` (und Repo-Anlage-Push) erst nach expliziter manueller
  Freigabe durch den User

## Arbeitsliste

Der vollständige Implementierungsplan mit Status steht in **`Agents.todo.md`** —
dort abhaken, was erledigt ist.

## TODO (UI-Review)

UI-Review-Screenshot-Skill noch nicht angewendet (Playwright-Harness + Vision-Analyse). Referenz: ocg-price-tracker/tests/screenshots (ui-screenshots.spec.ts mit Section-Captures).
