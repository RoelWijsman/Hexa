# Bouwplan HexaChem

*Webversie van ChemWindow 6.0 — opnieuw gebouwd, en beter.*
Schoolproject · opgesteld 13 november 2026

---

## 1. Doel

HexaChem wordt een programma in de browser waarmee je chemische structuren tekent, reactieschema's maakt en eigenschappen van moleculen berekent. ChemWindow 6.0 is het voorbeeld: alles wat dat programma goed doet, bouwen we na. Daarna maken we het beter: het werkt op elke computer zonder installatie, het ziet er modern uit en het kan dingen die ChemWindow nog niet kon.

**Klaar is het als:**
- je een molecuul kunt tekenen met atomen, bindingen en ringen;
- HexaChem de molecuulformule en molmassa uitrekent;
- je reactieschema's met pijlen en tekst kunt maken;
- je je werk kunt opslaan, openen en exporteren als afbeelding of PDF;
- het in Chrome, Edge en Firefox werkt.

---

## 2. Mappenstructuur

Alles staat in `ScheikundeProject`. We werken nooit buiten deze map.

```
ScheikundeProject/
├── oudesoftware/          ← ChemWindow 6.0 (alleen als voorbeeld; niet aanpassen)
└── HexaChem/              ← hier bouwen we de app (= de GitHub-repository)
    ├── CLAUDE.md          ← instructies voor Claude Code (wordt elke sessie gelezen)
    ├── .github/workflows/ ← GitHub Actions: ci.yml en deploy.yml
    ├── docs/
    │   ├── bouwplan.md        ← dit document
    │   ├── functielijst.md    ← alle functies van ChemWindow + status
    │   ├── logboek.md         ← wat je per dag gedaan hebt (handig voor je verslag)
    │   └── screenshots/       ← screenshots van ChemWindow ter vergelijking
    ├── public/            ← iconen, symbolen (glaswerk), lettertypen
    ├── src/
    │   ├── components/    ← knoppen, menubalk, werkbalk, dialoogvensters
    │   ├── editor/        ← het tekenvlak: atomen, bindingen, selecteren, zoomen
    │   ├── chemistry/     ← scheikunde-logica: formule, molmassa, valentie
    │   ├── templates/     ← ringen, functionele groepen, aminozuren
    │   ├── io/            ← opslaan, openen, exporteren
    │   ├── store/         ← de toestand van het document + ongedaan maken
    │   └── i18n/          ← alle Nederlandse teksten van de interface
    └── tests/
```

---

## 3. Techniek (de "bouwstenen")

| Onderdeel | Keuze | Waarom |
|---|---|---|
| Taal | **TypeScript** | JavaScript met typecontrole; Claude Code maakt er minder fouten in |
| Framework | **React** + **Vite** | Veel gebruikt, snel, veel voorbeelden online |
| Tekenvlak | **SVG** | Scherpe lijnen bij elke zoom, makkelijk te exporteren |
| Toestand | **Zustand** | Eenvoudig; past goed bij ongedaan maken/opnieuw |
| Scheikunde | **RDKit.js** | Gratis bibliotheek voor formules, molmassa, SMILES en MOL-bestanden |
| 3D-weergave | **3Dmol.js** | Molecuul draaien in 3D (extra, fase 8) |
| Testen | **Vitest** + **Playwright** | Automatisch controleren dat alles blijft werken |
| Versiebeheer | **Git** + **GitHub** (`gh` CLI) | Elke stap bewaard; je kunt altijd terug |
| Automatisch controleren | **GitHub Actions** | Bij elke wijziging draaien de tests vanzelf |
| Online zetten | **GitHub Pages** | Gratis; je docent kan het via een link openen |

**Nodig op je computer:** Node.js (LTS-versie), Git, GitHub CLI (`gh`), VS Code en Claude Code.

---

## 4. Werkwijze met Claude en Claude Code

**Wie doet wat**
- **Claude (deze chat):** plannen, uitleg over scheikunde en programmeren, ChemWindow analyseren, problemen doordenken, documenten schrijven.
- **Claude Code (terminal in de map HexaChem):** de code schrijven, uitvoeren, testen en fouten oplossen.
- **Jij:** beslissen wat er gebouwd wordt, alles testen in de browser, vergelijken met ChemWindow, en je logboek bijhouden.

**Vaste routine per sessie**
1. Open een terminal in `ScheikundeProject/HexaChem` en start `claude`.
2. Kies een **issue** op GitHub. Bouw **één onderdeel per keer** (bijvoorbeeld "dubbele bindingen tekenen").
3. Claude Code maakt een nieuwe branch voor dat issue.
4. Laat Claude Code bij grotere onderdelen eerst een plan maken (Shift+Tab → plan-modus), lees het, en keur het goed.
5. Test het zelf in de browser (`npm run dev`). Werkt het niet? Beschrijf precies wat je ziet.
6. Werkt het? Claude Code maakt commits, pusht en opent een **pull request** (PR).
7. GitHub Actions controleert de PR automatisch. Is alles groen, dan voeg jij de PR samen (merge). Het issue sluit dan vanzelf.
8. Schrijf twee regels in `docs/logboek.md`.

Zie hoofdstuk 6 voor de volledige GitHub-werkwijze.

**Tips**
- Hoe preciezer je vraag, hoe beter het resultaat. Noem wat je in ChemWindow ziet ("als ik in ChemWindow op een atoom dubbelklik, opent er een venster met …").
- Een screenshot van ChemWindow in `docs/screenshots/` helpt Claude Code enorm.
- Commit vaak. Is iets kapotgegaan, dan kun je terug.

---

## 5. Fasen

### Fase 0 — Voorbereiding en analyse
- Node.js, Git, VS Code en Claude Code installeren.
- Map `HexaChem` aanmaken met `CLAUDE.md` en `docs/`.
- **ChemWindow 6.0 uitpluizen:** elk menu, elke werkbalk en elk dialoogvenster openen en screenshotten. Alles opschrijven in `docs/functielijst.md`.
- Elke functie een prioriteit geven:
  - **Moet** — zonder dit is het geen ChemWindow (tekenen, ringen, molmassa, opslaan)
  - **Zou moeten** — belangrijk, maar het kan zonder (glaswerksymbolen, PDF-export)
  - **Kan** — extra's (3D, donkere modus)
  - **Niet** — bewust weglaten (bijv. verouderde Windows-functies)

**Resultaat:** een complete functielijst met prioriteiten.

### Fase 1 — Fundament
- Project opzetten met Vite + React + TypeScript; Git en GitHub koppelen.
- Het scherm indelen zoals ChemWindow: menubalk boven, gereedschapspalet links, tekenvlak in het midden, statusbalk onder.
- Tekenvlak met zoomen, schuiven en een optioneel raster.

**Resultaat:** een lege app die eruitziet als een tekenprogramma.

### Fase 2 — Kern: moleculen tekenen
- Atomen plaatsen (standaard C; elementsymbool typen om te wijzigen).
- Bindingen: enkel, dubbel, drievoudig, wig (naar voren) en gestreept (naar achteren).
- Bindingen tekenen met vaste hoeken (120°-zigzag zoals in ChemWindow).
- Selecteren, verplaatsen, draaien, spiegelen en verwijderen.
- **Ongedaan maken / opnieuw** (Ctrl+Z / Ctrl+Y).
- Ladingen en vrije elektronenparen.

**Resultaat:** je kunt ethanol, azijnzuur en benzeen tekenen.

### Fase 3 — Sjablonen
- Ringen: cyclopropaan t/m cyclooctaan en benzeen.
- Functionele groepen: –OH, –COOH, –NH₂, –CHO, enzovoort.
- Bibliotheken: aminozuren, suikers, nucleobasen.
- Sjabloon op een atoom of binding klikken = vastgemaakt aan je molecuul.

### Fase 4 — Scheikundige intelligentie
- RDKit.js toevoegen.
- Automatisch berekenen: **molecuulformule, molmassa, exacte massa en elementsamenstelling (%)**.
- Valentiecontrole: een C met 5 bindingen wordt rood gemarkeerd.
- Waterstofatomen automatisch tonen bij heteroatomen (OH, NH₂).
- Importeren en exporteren als **SMILES** en **MOL/SDF**.
- Naam → structuur via PubChem ("aspirine" typen → molecuul verschijnt).

### Fase 5 — Tekst, pijlen en reactieschema's
- Tekstvak met subscript en superscript (H₂O, Fe³⁺).
- Pijlen: reactiepijl, evenwichtspijl, resonantiepijl en gebogen elektronenpijl.
- Plusteken, haken, kleuren en lijndiktes.
- Uitlijnen en groeperen van objecten.

### Fase 6 — Laboratoriumsymbolen
- Een bibliotheek met glaswerk als SVG: bekerglas, erlenmeyer, rondbodemkolf, koeler, brander, statief, scheitrechter, enzovoort.
- Slepen naar het tekenvlak, schalen en verbinden tot een opstelling (bijvoorbeeld destillatie).

### Fase 7 — Bestanden
- Opslaan en openen in een eigen formaat (`.hexa`).
- Automatisch tussentijds opslaan in de browser, zodat er niets verloren gaat.
- Exporteren als **PNG, SVG en PDF**; afdrukken.
- Kopiëren naar het klembord om in Word of PowerPoint te plakken.

### Fase 8 — Beter dan ChemWindow
Kies er een paar uit:
- 3D-weergave van je molecuul (3Dmol.js).
- Interactief periodiek systeem.
- Stoichiometrie-rekenhulp bij een reactieschema.
- Donkere modus.
- Werkt offline (installeerbaar als app, PWA).
- Aanraakscherm/tablet-ondersteuning.
- Sneltoetsenoverzicht (druk op `?`).

### Fase 9 — Testen, afwerken en presenteren
- Automatische tests voor de scheikunde-berekeningen (klopt de molmassa van glucose?).
- Gebruikerstest: laat drie klasgenoten een opdracht uitvoeren en noteer waar ze vastlopen.
- Bugs oplossen; HexaChem online zetten.
- Verslag en presentatie: ChemWindow vs. HexaChem naast elkaar.

---

## 6. GitHub-werkwijze

Claude Code doet al het GitHub-werk met de `gh`-opdrachtregel. Jij hoeft alleen pull requests te bekijken en samen te voegen.

**Repository**
- Repository: **https://github.com/RoelWijsman/Hexa**. Claude Code koppelt de map `ScheikundeProject/HexaChem` eraan als `origin` en maakt geen nieuwe repository aan.
- `oudesoftware` komt **niet** in de repository: die map staat erbuiten, en ChemWindow is software van een ander bedrijf.
- `main` werkt altijd. Er wordt nooit rechtstreeks op `main` gebouwd.

**Issues, milestones en het bord**
- Elke regel uit `docs/functielijst.md` wordt een **issue** (bijv. "#14 Drievoudige binding tekenen").
- Elke fase wordt een **milestone** ("Fase 2 — Moleculen tekenen"). Zo zie je per fase hoeveel er af is.
- Labels: `moet`, `zou-moeten`, `kan`, `bug`, `chemie`, `ui`.
- Een **GitHub Project-bord** met de kolommen *Te doen → Bezig → Klaar*.

**Branches, commits en pull requests**
- Per issue een branch: `feat/14-drievoudige-binding` of `fix/21-zoom-springt`.
- Commitberichten volgens Conventional Commits: `feat: drievoudige binding tekenen`, `fix: …`, `docs: …`, `test: …`.
- Kleine commits met één verandering per commit.
- De PR-beschrijving zegt wat er veranderd is, hoe je het test, en `Closes #14`.
- Samenvoegen gebeurt met *squash and merge*. Daarna wordt de branch verwijderd.

**Automatisch (GitHub Actions)**
- **CI** bij elke PR: typecontrole, lint, tests en build. Is iets rood, dan wordt de PR niet samengevoegd.
- **Deploy** bij elke merge naar `main`: HexaChem komt automatisch online op GitHub Pages (`https://roelwijsman.github.io/Hexa/`).

**Versies**
Aan het eind van elke fase maakt Claude Code een **release** met een korte lijst van wat nieuw is.

| Fase | Versie | Mijlpaal |
|---|---|---|
| 0 · Voorbereiding | — | Functielijst klaar, issues aangemaakt |
| 1 · Fundament | v0.1.0 | App met layout staat online |
| 2 · Tekenen | v0.2.0 | Benzeen tekenen werkt |
| 3 · Sjablonen | v0.3.0 | Ringen en groepen |
| 4 · Chemie | v0.4.0 | Molmassa klopt |
| 5 · Reacties | v0.5.0 | Reactieschema compleet |
| 6 · Labsymbolen | v0.6.0 | Destillatieopstelling tekenen |
| 7 · Bestanden | v0.7.0 | Opslaan en PDF-export |
| 8 · Extra's | v0.8.0 | Iets wat ChemWindow niet kan |
| 9 · Afronden | **v1.0.0** | Getest, af en gepresenteerd |

Er is geen deadline. We gaan pas naar de volgende fase als de "Moet"-issues van de huidige fase af zijn.

---

## 7. Risico's en oplossingen

| Risico | Oplossing |
|---|---|
| Het tekenvlak wordt te ingewikkeld | Fase 2 klein beginnen: eerst alleen enkele bindingen, dan uitbreiden |
| RDKit.js is groot of lastig | Eerst zelf molmassa uitrekenen met een tabel van atoommassa's; RDKit pas daarna |
| Te veel willen | De prioriteiten uit fase 0 aanhouden: eerst alle "Moet", dan pas de rest |
| Code raakt kwijt of kapot | Na elk werkend onderdeel een Git-commit |
| Claude Code begrijpt niet wat je bedoelt | Screenshot van ChemWindow + precieze beschrijving |

---

## 8. Eerste stappen (vandaag/morgen)

1. Controleer of alles werkt: `node -v`, `git --version`, `gh auth status` en `claude --version`.
2. Maak de repository aan op GitHub, leeg, zonder README.
3. Vul de link in op de regel "Repository:" in CLAUDE.md, of stuur hem naar Claude in de chat.
4. Maak de map `ScheikundeProject/HexaChem` met daarin de map `docs`. Zet `CLAUDE.md` en dit bouwplan erin.
5. Open ChemWindow 6.0 en maak screenshots van alle menu's (fase 0).
6. Start Claude Code in `HexaChem` met:
   > *Lees CLAUDE.md en docs/bouwplan.md. Zet de GitHub-repository op volgens hoofdstuk 6: git init, .gitignore, een eerste commit, koppelen aan de bestaande repository uit CLAUDE.md en pushen, labels en milestones per fase aanmaken, en een Project-bord. Maak eerst een plan.*
7. Daarna:
   > *Begin met fase 1: zet een Vite + React + TypeScript-project op en maak de layout van ChemWindow na (menubalk, gereedschapspalet links, tekenvlak, statusbalk). Voeg de CI- en deploy-workflows van GitHub Actions toe. Werk via een branch en een pull request.*
