# HexaChem

HexaChem is een webversie van ChemWindow 6.0 (programma om chemische structuren te tekenen), gebouwd als schoolproject. Doel: alle belangrijke functies van ChemWindow nabouwen en het daarna beter maken.

## Regels
- Werk alleen in de map `ScheikundeProject/HexaChem`.
- `ScheikundeProject/oudesoftware` is ChemWindow 6.0: alleen als voorbeeld gebruiken, **nooit aanpassen**.
- Communiceer in het Nederlands; code, namen van variabelen en commits in het Engels.
- **De interface van de app is volledig Nederlands** (menu's, knoppen, tooltips, foutmeldingen). Zet alle teksten in één bestand (`src/i18n/nl.ts`) en zet ze niet los in componenten.
- Gebruik Nederlandse scheikundige termen zoals in de Nederlandse lesmethodes: molaire massa, molecuulformule, structuurformule, enkele/dubbele/drievoudige binding.
- Bouw één onderdeel per keer. Maak bij grotere onderdelen eerst een plan.
- De ontwikkelaar kan goed programmeren: wees technisch en beknopt, leg geen basisdingen uit, maar licht ontwerpkeuzes en afwegingen wel kort toe.
- Na elk werkend onderdeel: tests draaien en een commit maken.

## GitHub-werkwijze (altijd volgen)
Repository: https://github.com/RoelWijsman/Hexa. Deze bestaat al: koppel hem als `origin` en maak geen nieuwe repository aan.
Let op: de repository heet `Hexa`, de app heet **HexaChem**. GitHub Pages draait op `https://roelwijsman.github.io/Hexa/`, dus zet in Vite `base: '/Hexa/'`.
Gebruik de `gh` CLI voor al het GitHub-werk.
- **Nooit rechtstreeks op `main` werken of pushen.** `main` moet altijd werken.
- Begin elk onderdeel vanuit een issue. Bestaat er nog geen issue, maak er dan een aan (met label en milestone van de fase).
- Branch per issue vanaf een bijgewerkte `main`: `feat/<nr>-<korte-naam>`, `fix/<nr>-<korte-naam>`, `docs/…`, `test/…`.
- Commitberichten in Conventional Commits, in het Engels: `feat: add triple bond tool`. Maak kleine commits.
- Draai vóór elke push: typecheck, lint, tests en build. Alles moet slagen.
- Push en open een pull request met `gh pr create`: wat er veranderd is, hoe je het test, en `Closes #<nr>`.
- **Voeg de PR niet zelf samen.** De ontwikkelaar test en merget (squash and merge). Pas daarna mag de volgende branch beginnen.
- Nooit `git push --force` naar `main`, nooit geschiedenis herschrijven op gedeelde branches, nooit geheimen (API-sleutels, wachtwoorden) committen.
- Aan het eind van een fase: een tag en een GitHub-release (`v0.<fase>.0`) met een changelog.
- GitHub Actions: `ci.yml` (typecheck, lint, test, build bij elke PR) en `deploy.yml` (GitHub Pages bij een push naar `main`).
- `oudesoftware/` komt nooit in de repository.
- Werk na elk samengevoegd onderdeel `docs/functielijst.md` bij.

## Techniek
- Vite + React + TypeScript
- Tekenvlak in SVG
- Toestand met Zustand (met undo/redo via een geschiedenis van commando's)
- Scheikunde met RDKit.js (formule, molmassa, SMILES, MOL)
- Tests: Vitest (logica) en Playwright (in de browser)

## Mappen
- `src/components/` — UI: menubalk, werkbalk, dialogen
- `src/editor/` — tekenvlak: atomen, bindingen, selectie, zoom
- `src/chemistry/` — scheikunde-logica
- `src/templates/` — ringen, functionele groepen, bibliotheken
- `src/io/` — opslaan, openen, exporteren
- `src/store/` — documenttoestand
- `src/i18n/` — alle Nederlandse interfaceteksten
- `docs/` — bouwplan, functielijst, logboek, screenshots van ChemWindow

## Commando's
- `npm run dev` — app starten
- `npm test` — tests draaien
- `npm run lint` — codestijl controleren
- `npm run typecheck` — typecontrole
- `npm run build` — productieversie bouwen

## Huidige stand
Zie `docs/bouwplan.md` (fasen) en `docs/functielijst.md` (wat af is).
